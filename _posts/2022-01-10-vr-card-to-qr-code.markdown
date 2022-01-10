---
layout: post
title:  Generating QR Code for the vCard
description: Let's generate a QR code using your contact information. We will learn today about QR codes and a use case of generating a personalized QR code that you can share with people without having to share it using traditional way.
date:   2022-01-10 10:00:00 +0500
categories: qr qr-code python vCard
---

Let's generate a QR code using your contact information. We will learn today about QR codes and a use case of generating a personalized QR code that you can share with people without having to share it using traditional way.


# Use case

Recently someone asked me for my contact details and i felt it traditional when vocally saying my phone number/email while people around us. I was looking for solutions to solve this problem in this modern world and i encountered QR codes. I want to use a standard format for sharing the contact information and found vCard as the answer.

# What is a QR Code ?

A QR code (an initialism for Quick Response code) is a type of matrix barcode (or two-dimensional barcode) invented in 1994 by the Japanese automotive company Denso Wave. A barcode is a machine-readable optical label that can contain information about the item to which it is attached. In practice, QR codes often contain data for a locator, identifier, or tracker that points to a website or application. A QR code uses four standardized encoding modes (numeric, alphanumeric, byte/binary, and kanji) to store data efficiently; extensions may also be used.

The Quick Response system became popular outside the automotive industry due to its fast readability and greater storage capacity compared to standard UPC barcodes. Applications include product tracking, item identification, time tracking, document management, and general marketing.

A QR code consists of black squares arranged in a square grid on a white background, which can be read by an imaging device such as a camera, and processed using Reed–Solomon error correction until the image can be appropriately interpreted. The required data is then extracted from patterns that are present in both horizontal and vertical components of the image.

# What is a vCard ?

vCard, also known as VCF (Virtual Contact File), is a file format standard for electronic business cards. vCards can be attached to e-mail messages, sent via Multimedia Messaging Service (MMS), on the World Wide Web, instant messaging or through QR code. They can contain name and address information, phone numbers, e-mail addresses, URLs, logos, photographs, and audio clips.

vCard is used as data interchange format in smartphone contacts, personal digital assistants (PDAs), personal information managers (PIMs) and customer relationship management (CRMs). To accomplish these data interchange applications, other "vCard variants" have been used and proposed as "variant standards", each for its specific niche: XML representation, JSON representation, or web pages. vCard Plus, an elevated vCard, makes use of a customized landing page with all the basic information along with a profile photo, maps location and other fields. This can also be saved as a contact file on smartphones.

# Generating vCard

First i want to get a template for vCard so that i can use it to generate QR code and the below standard for vCard 4.0

```python
template = """BEGIN:VCARD
VERSION:4.0
N:{last_name};{first_name};;{name_prefix}.;
ORG:{company}
TITLE:{title}
PHOTO;MEDIATYPE#image/gif:{image_url}
TEL;TYPE#work,voice;VALUE#uri:tel:{work_phone}
TEL;TYPE#home,voice;VALUE#uri:tel:{home_phone}
ADR;TYPE#WORK;PREF#1;LABEL#"{work_address_label}":;;{work_address_line_1};{work_address_city};{work_address_stage_code};{work_address_pin};{work_address_country}
ADR;TYPE#HOME;LABEL#"{home_address_label}":;;{home_address_line_1};{home_address_city};{home_address_stage_code};{home_address_pin};{home_address_country}
EMAIL:{email}
REV:20080424T195243Z
x-qq:21588891
END:VCARD"""
```

Here is some sample details that i can format using the above template

```python
details = {
    "last_name": "Khan",
    "first_name": "Afroze",
    "name_prefix": "Mr.",
    "company": "proxyroot",
    "title": "Engineering Leader",
    "image_url": "https://proxyroot.com",
    "work_phone": "+1-917-000-0000",
    "home_phone": "+1-917-000-0001",
    "work_address_label": "proxyroot",
    "work_address_line_1": "proxyroot inc",
    "work_address_city": "Azgard City",
    "work_address_stage_code": "AZ",
    "work_address_pin": "00000",
    "work_address_country": "Azgard",
    "home_address_label": "Holmes",
    "home_address_line_1": "221B Baker Street",
    "home_address_city": "London",
    "home_address_stage_code": "11111",
    "home_address_pin": "NW1 6XE",
    "home_address_country": "United Kingdom",
    "email": "afroze@proxyroot.com",
}

>>> template.format(**details)
'BEGIN:VCARD\nVERSION:4.0\nN:Khan;Afroze;;Mr..;\nORG:proxyroot\nTITLE:Engineering Leader\nPHOTO;MEDIATYPE#image/gif:https://proxyroot.com\nTEL;TYPE#work,voice;VALUE#uri:tel:+1-917-000-0000\nTEL;TYPE#home,voice;VALUE#uri:tel:+1-917-000-0001\nADR;TYPE#WORK;PREF#1;LABEL#"proxyroot":;;proxyroot inc;Azgard City;AZ;00000;Azgard\nADR;TYPE#HOME;LABEL#"Holmes":;;221B Baker Street;London;11111;NW1 6XE;United Kingdom\nEMAIL:afroze@proxyroot.com\nREV:20080424T195243Z\nx-qq:21588891\nEND:VCARD'
>>>
```

# Generating QR code

I found [qrcode](https://pypi.org/project/qrcode/) package to be easy and simple to use. Here is how i can generate a qr code for any data

```python
import qrcode


def create_qr_code_image(qr_code_data, file_path):
    qr = qrcode.QRCode(
        version=1,
        error_correction=qrcode.constants.ERROR_CORRECT_L,
        box_size=10,
        border=2,
    )
    qr.add_data(qr_code_data)
    qr.make(fit=True)
    img = qr.make_image(fill_color="black", back_color="white")
    img.save(file_path)

```

***A few things to keep in mind about the QRCode class arguments:***

The error_correction parameter controls the error correction used for the QR Code. The following four constants are made available on the qrcode package

- ERROR_CORRECT_L
- - About 7% or less errors can be corrected.
- ERROR_CORRECT_M (default)
- - About 15% or less errors can be corrected.
- ERROR_CORRECT_Q
- - About 25% or less errors can be corrected.
- ERROR_CORRECT_H.
- - About 30% or less errors can be corrected.

The box_size parameter controls how many pixels each “box” of the QR code is.

The border parameter controls how many boxes thick the border should be (the default is 4, which is the minimum according to the specs).

Now let me use the above method to save the qr code

```python
data = template.format(**details)
create_qr_code_image(data, "my-vrcard-qr-code.png")
```

The above code outputed the below image

![image](https://user-images.githubusercontent.com/21299746/148818282-48410361-5c01-4fd2-9b4a-a6dba4b5f9e4.png)

# Conclusion

QR codes are awesome! You can use it to store variety of data as mentioned in the beginning of the post.
