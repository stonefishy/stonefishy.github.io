---
title: How to pricing the Azure Speech to Speech service
date: 2025-08-26 10:28:50
categories: AI/ML
tags: [AI, Azure, Cloud]
---

In previous blog [Speech Translator Based on Azure Speech Service](https://stonefishy.github.io/2025/06/19/speech-translator-base-on-azure-speech-service/), we talked about the architecture and implementation details of a speech translation service using Azure's Speech API. In this blog, we will focus on the pricing aspects of the Azure Speech to Speech service.

To calculate the Azure Speech to Speech Translation price, reference the Azure Pricing page: [Azure AI Speech Pricing | Microsoft Azure](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/)

The Azure Speech to Speech service involves two main components: Speech Translation and Text to Speech. Below, we will break down the pricing for each component and provide an example calculation for translating 6 hours of Chinese audio to English audio.

### Pricing Summary
The Azure Speech to Speech service combines two main features: Speech Translation and Text to Speech. Below is a summary of the pricing for each feature.

| Feature                | Tier         | Included/Rate                |
|------------------------|--------------|------------------------------|
| Speech Translation     | Free (F0)    | 5 audio hours/month free     |
|                        | Pay as You Go| $2.5 per audio hour          |
| Text to Speech (Neural)| Free (F0)    | 0.5M characters/month free   |
|                        | Pay as You Go| $15 per 1M characters        |
| Text to Speech (Neural HD)| Pay as You Go| $30 per 1M characters     |

---

### Example Calculation

Assume we are translating 6 Hours of Chinese Audio to English Audio, the Azure Speech to Speech translation include below two steps

#### **Step-by-Step Process for Real-Time Translation**
1. **Speech Translation**:  
   The Chinese audio is processed and converted into English text using Azure's Speech Translation service. This step is billed based on the duration of the audio.

2. **Text to Speech**:  
   The translated English text is synthesized into English audio using Azure's Text to Speech service. This step is billed based on the number of characters in the translated text.

---

#### **Step 1: Speech Translation**
- **Free Tier**: 5 audio hours are free per month.
- **Pay as You Go Tier**: $2.5 per audio hour for additional hours.

For 6 hours:
- 5 hours are free.
- 1 hour is charged at $2.5/hour.

**Speech Translation Cost**:  
`1 hour × $2.5 = $2.5`

---

#### **Step 2: Text to Speech**
Assume the translated English audio requires **1M characters** for synthesis:
- **Free Tier**: 0.5M characters are free per month.
- **Pay as You Go Tier**: $15 per 1M characters (Neural).

For 1M characters:
- 0.5M characters are free.
- 0.5M characters are charged at $15 per 1M characters.

**Text to Speech Cost**:  
`0.5M characters × $15 = $7.5`

---

### **Total Cost**
- Speech Translation: $2.5  
- Text to Speech: $7.5  

**Total Example Cost**:  
`$2.5 + $7.5 = $10.0`


### Summary
In this example, translating 6 hours of Chinese audio to English audio using Azure's Speech to Speech service would cost approximately $10.0. This calculation assumes the use of the Pay as You Go tier for both services after utilizing the free tier allowances. Actual costs may vary based on the specific usage and any additional features or services used. Always refer to the [Azure Pricing page](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/) for the most up-to-date pricing information.