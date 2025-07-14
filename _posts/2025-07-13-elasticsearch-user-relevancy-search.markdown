---
layout: post
title: "Building Relevancy-Based User Search with Elasticsearch"
description: "Learn how to build a weighted scoring system for user search using Elasticsearch with periodic data aggregation and bulk indexing"
date: 2025-07-13
categories: [elasticsearch, search-algorithms, django, celery]
tags: [elasticsearch, weighted-scoring, user-search, bulk-indexing, relevancy]
---

# Building Relevancy-Based User Search with Elasticsearch

Searching users based on multiple weighted factors is a common challenge in social platforms and marketplaces. This article explores how to build a sophisticated user search system using Elasticsearch with weighted scoring algorithms for factors like location proximity, network connections, past interactions, and financial transactions.

## Problem Statement

At Spice, we needed to search users based on multiple relevancy factors:
- **Location proximity** - How close users are geographically
- **Network connections** - Direct and indirect connections
- **Past interactions** - Likes, comments, messages
- **Financial transactions** - Money transfers between users
- **Activity level** - Recent engagement and activity

Each factor has different importance (weights) and we need to combine them into a single relevancy score for fast, accurate search results.

## System Architecture

```mermaid
graph TB
    A[Django Models] --> B[Celery Task]
    B --> C[Data Aggregation]
    C --> D[Elasticsearch Index]
    E[Search Request] --> F[Weighted Scoring]
    F --> D
    D --> G[Ranked Results]
    
    subgraph "Data Sources"
        H[User Profile]
        I[Network Connections]
        J[Interactions]
        K[Transactions]
    end
    
    H --> C
    I --> C
    J --> C
    K --> C
```

## Data Models

### Django Models Structure

```python
# Core models for user data
class User(AbstractUser):
    bio = models.TextField()
    location = models.CharField()
    latitude = models.DecimalField()
    longitude = models.DecimalField()
    profile_completeness = models.IntegerField()
    last_active = models.DateTimeField()

class UserConnection(models.Model):
    from_user = models.ForeignKey(User)
    to_user = models.ForeignKey(User)
    connection_type = models.CharField()  # direct, mutual, pending
    strength = models.FloatField()  # 0-1 connection strength

class UserInteraction(models.Model):
    from_user = models.ForeignKey(User)
    to_user = models.ForeignKey(User)
    interaction_type = models.CharField()  # like, comment, message
    created_at = models.DateTimeField()

class Transaction(models.Model):
    from_user = models.ForeignKey(User)
    to_user = models.ForeignKey(User)
    amount = models.DecimalField()
    created_at = models.DateTimeField()
```

## Elasticsearch Document Structure

```python
# Elasticsearch document for aggregated user data
class UserDocument(Document):
    # Basic fields
    username = fields.TextField(analyzer='text_analyzer')
    first_name = fields.TextField()
    last_name = fields.TextField()
    bio = fields.TextField()
    location = fields.TextField()
    location_coords = fields.GeoPointField()
    
    # Aggregated metrics
    connection_count = fields.IntegerField()
    mutual_connections = fields.IntegerField()
    interaction_count = fields.IntegerField()
    transaction_count = fields.IntegerField()
    total_transaction_amount = fields.FloatField()
    
    # Network and activity scores
    network_proximity = fields.FloatField()
    connection_strength = fields.FloatField()
    profile_completeness = fields.IntegerField()
    last_active_days = fields.IntegerField()
    activity_score = fields.FloatField()
    
    # Pre-calculated relevancy factors
    location_score = fields.FloatField()
    network_score = fields.FloatField()
    interaction_score = fields.FloatField()
    transaction_score = fields.FloatField()
    activity_score_calc = fields.FloatField()
```

## Weighted Scoring Algorithm

The core algorithm combines multiple factors with configurable weights:

```python
class SearchWeights:
    location_weight = 0.25      # Geographic proximity
    network_weight = 0.30       # Connection strength
    interaction_weight = 0.20   # Past interactions
    transaction_weight = 0.15   # Financial transactions
    activity_weight = 0.10      # Recent activity

def calculate_total_score(scores):
    return (
        scores['location'] * 0.25 +
        scores['network'] * 0.30 +
        scores['interaction'] * 0.20 +
        scores['transaction'] * 0.15 +
        scores['activity'] * 0.10
    )
```

### Scoring Factors

1. **Location Score**: Exponential decay based on distance
   ```
   score = exp(-distance_km / 50.0)
   ```

2. **Network Score**: Connection count, mutual connections, and strength
   ```
   network_score = (connection_score * 0.4) + (mutual_score * 0.4) + (strength_score * 0.2)
   ```

3. **Interaction Score**: Logarithmic scale for total interactions + recent activity bonus
   ```
   interaction_score = log10(interaction_count + 1) / 3.0 + (recent_interactions / 50.0)
   ```

4. **Transaction Score**: Count and amount-based scoring
   ```
   transaction_score = (count_score * 0.6) + (amount_score * 0.4)
   ```

5. **Activity Score**: Profile completeness, recency, and engagement
   ```
   activity_score = (profile_score * 0.3) + (recency_score * 0.3) + (engagement_score * 0.4)
   ```

## Periodic Data Aggregation

### Celery Task for Bulk Indexing

```python
@shared_task
def bulk_index_users():
    """Periodically aggregate and index user data"""
    users = User.objects.select_related().prefetch_related(
        'connections_from', 'connections_to',
        'interactions_from', 'interactions_to',
        'transactions_from', 'transactions_to'
    )
    
    for user in users:
        # Calculate aggregated metrics
        connection_count = user.connections_from.count() + user.connections_to.count()
        mutual_connections = calculate_mutual_connections(user)
        interaction_count = user.interactions_from.count() + user.interactions_to.count()
        transaction_amount = calculate_total_transactions(user)
        
        # Calculate relevancy scores
        scores = calculate_relevancy_scores(user)
        
        # Update Elasticsearch document
        UserDocument.update(user)
```

### Data Aggregation Process

```mermaid
flowchart TD
    A[Start Aggregation] --> B[Fetch Users with Relations]
    B --> C[Calculate Connection Metrics]
    C --> D[Calculate Interaction Metrics]
    D --> E[Calculate Transaction Metrics]
    E --> F[Calculate Activity Metrics]
    F --> G[Compute Relevancy Scores]
    G --> H[Update Elasticsearch Document]
    H --> I{More Users?}
    I -->|Yes| B
    I -->|No| J[Complete]
```

## Elasticsearch Index Creation

```python
# Index settings for optimal search performance
index_settings = {
    'number_of_shards': 3,
    'number_of_replicas': 1,
    'refresh_interval': '30s',
    'analysis': {
        'analyzer': {
            'text_analyzer': {
                'type': 'custom',
                'tokenizer': 'standard',
                'filter': ['lowercase', 'stop', 'snowball']
            }
        }
    }
}

# Create index with mappings
UserDocument._index.create()
```

## Search Implementation

### Django Elasticsearch DSL Query

```python
def search_users(query=None, location=None, radius_km=50, weights=None):
    search = Search(index='users')
    
    # Text search
    if query:
        search = search.query(
            'multi_match',
            query=query,
            fields=['username^3', 'first_name^2', 'last_name^2', 'bio']
        )
    
    # Location-based search
    if location:
        search = search.query(
            'geo_distance',
            location_coords=location,
            distance=f'{radius_km}km'
        )
    
    # Execute and score results
    response = search.execute()
    
    # Apply weighted scoring
    for hit in response:
        scores = calculate_factor_scores(hit.to_dict(), location)
        hit.total_score = calculate_total_score(scores)
    
    return sorted(response, key=lambda x: x.total_score, reverse=True)
```

## Sample Data and Results

### Input Data Example

```json
{
  "users": [
    {
      "id": 1,
      "username": "john_developer",
      "location": "San Francisco, CA",
      "latitude": 37.7749,
      "longitude": -122.4194,
      "connection_count": 45,
      "mutual_connections": 12,
      "interaction_count": 156,
      "transaction_count": 8,
      "total_transaction_amount": 1250.00,
      "profile_completeness": 85,
      "last_active_days": 2
    },
    {
      "id": 2,
      "username": "jane_designer",
      "location": "Oakland, CA",
      "latitude": 37.8044,
      "longitude": -122.2711,
      "connection_count": 32,
      "mutual_connections": 8,
      "interaction_count": 89,
      "transaction_count": 3,
      "total_transaction_amount": 450.00,
      "profile_completeness": 92,
      "last_active_days": 1
    }
  ]
}
```

### Search Results

**Query**: "developer" near San Francisco

```json
{
  "results": [
    {
      "id": 1,
      "username": "john_developer",
      "relevancy_scores": {
        "location": 0.95,
        "network": 0.78,
        "interaction": 0.82,
        "transaction": 0.65,
        "activity": 0.88
      },
      "total_relevancy_score": 0.82,
      "rank": 1
    },
    {
      "id": 2,
      "username": "jane_designer",
      "relevancy_scores": {
        "location": 0.85,
        "network": 0.62,
        "interaction": 0.58,
        "transaction": 0.42,
        "activity": 0.92
      },
      "total_relevancy_score": 0.71,
      "rank": 2
    }
  ]
}
```

## Performance Optimization

### Caching Strategy

```python
# Cache search results for 5 minutes
cache_key = f"search:{hash(search_params)}"
cached_results = cache.get(cache_key)
if cached_results:
    return cached_results

results = perform_search(search_params)
cache.set(cache_key, results, 300)
```

### Query Optimization

```python
# Optimized queryset with annotations
users = User.objects.annotate(
    connection_count=Count('connections_from') + Count('connections_to'),
    interaction_count=Count('interactions_from') + Count('interactions_to'),
    transaction_count=Count('transactions_from') + Count('transactions_to')
).select_related().prefetch_related(
    'connections_from', 'connections_to',
    'interactions_from', 'interactions_to'
)
```

## Monitoring and Analytics

```python
# Search performance metrics
metrics = {
    'avg_response_time_ms': 45,
    'cache_hit_rate': 0.78,
    'success_rate': 0.995,
    'top_search_terms': [
        {'term': 'developer', 'count': 1250},
        {'term': 'designer', 'count': 980}
    ]
}
```

## Conclusion

This implementation provides a robust user search system with:

- **Weighted scoring** for multiple relevancy factors
- **Periodic bulk indexing** for performance
- **Geographic search** with distance-based scoring
- **Network-aware** results based on connections
- **Activity-based** relevancy scoring
- **Caching** for sub-second response times

The system can handle thousands of users efficiently while providing personalized, relevant search results based on multiple weighted factors.

### Key Benefits

- **Scalable**: Elasticsearch handles large datasets
- **Flexible**: Easy to adjust weights and add factors
- **Fast**: Caching and optimized queries
- **Accurate**: Multi-factor scoring provides relevant results
- **Maintainable**: Clean separation of concerns

This solution addresses real-world challenges in user discovery and provides a solid foundation for building sophisticated search features in social platforms and marketplaces. 