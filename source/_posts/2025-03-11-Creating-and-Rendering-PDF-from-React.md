---
title: Creating and Rendering a PDF from React Easily
date: 2025-03-11 09:53:13
categories: Frontend
tags: [JavaScript, React, Next.js, PDF]
---

Creating or rendering a PDF file in a web page is a common requirement. It allows user to download the pdf file and view it offline in some scenarios. Today, we will learn how to render and generate a PDF file from React using the `react-pdf` library.

The `react-pdf` library is a React component that allows us to design and render PDF documents in React. Let's get started an example, it's very simple to create a pdf and render it in React.

## Install the `react-pdf` Library

``` bash
npm install @react-pdf/renderer --save
```

## Create a PDF Document

We will create a simple PDF document using the `react-pdf` library. It supports customize styles and Flex layout to create the PDF.  Below code example using `react-pdf` library to create a PDF document, including using `Document`, `Page`, `StyleSheet`, `Image`, `View`, `Text`, `Link` components.

``` jsx
'use client';

import React from 'react';
import {Document, Page, StyleSheet, Image, View, Text, Link} from '@react-pdf/renderer';

const styles = StyleSheet.create({
    page: {
        flexDirection: 'column',
        backgroundColor: 'white',
        padding: "20px",
    },
    section: {
        marginBottom: "30px",
    },
    header: {
        textAlign: "center",
        fontSize: "24px",
        fontWeight: "bold",
        marginBottom: "30px",
    },
    logo: {
        height: "90px",
        width: "100px",
        margin: "20px auto",
    },
    title: {
        fontSize: 18,
        fontWeight: "bold",
        marginBottom: "10px",
    },
    text: {
        fontSize: "14px",
        marginBottom: "10px",
    },
    code: {
        fontSize: "14px",
        backgroundColor: "#3e3e3e",
        color: "#fff",
        padding: "20px",
        marginBottom: "10px",
        borderRadius: "5px",
    }
});

const PdfDocument = () => {
    return (
    <Document title="My PDF Document" author="Andrewsy" subject="This is a sample PDF document">
        <Page size="A4" style={styles.page}>
            <View style={styles.section}>
                <Text style={styles.header}>React PDF Renderer</Text>
                <Text style={styles.text}>The react-pdf library allows you to render PDF documents using React components.</Text>
                <Image style={styles.logo} src="images/logo.png"/>
            </View>
            <View style={styles.section}>
                <Text style={styles.title}>Install react-pdf library</Text>
                <Text style={styles.text}>To install the react-pdf library, you can use npm or yarn:</Text>
                <Text style={styles.code}>npm install @react-pdf/renderer</Text>
                <Text></Text>
                <Text style={styles.code}>yarn add @react-pdf/renderer</Text>
                <Text style={styles.text}>For component documentation, visit the official website <Link src="https://react-pdf.org/">@react-pdf/renderer</Link>.</Text>
            </View>
        </Page>
    </Document>
  );
};

export default PdfDocument;
```

From above, we can see that we have created a PDF document using `react-pdf` library. We have used `Document` and `Page` components to create the PDF document. We have also used `StyleSheet` to define the styles for the PDF document. And using `Image`, `View`, `Text`, `Link` components to create the content of the PDF document.

## View PDF Document

To view the PDF document, we need to use `PDFViewer` component to render the PDF document in the browser. There is one thing need notice, if your project is using `Next.js`, and you want to use `PDFViewer` or `PDFDownloadLink` component in client side render directly, you will get below error:

``` error
[Error: PDFViewer is a web specific API. You're either using this component on Node, or your bundler is not loading react-pdf from the appropriate web build.]
```

To fixed the issue in `Next.js` project. Use Next dynamic function to manually set server-side rendering off and import it through that function instead of the regular import

``` jsx
"use client";

import dynamic from "next/dynamic";

const PDFViewer = dynamic(() => import("@react-pdf/renderer").then((mod) => mod. PDFViewer),{ssr: false });
const PDFDownloadLink = dynamic(() => import("@react-pdf/renderer").then((mod) => mod. PDFDownloadLink),{ssr: false });
```

instead of

``` jsx
"use client";

import { PDFViewer, PDFDownloadLink } from "@react-pdf/renderer";

```

The view PDF document code is below, for the `PDFViewer` component, we need to set the width and height of the PDF document. Actually it is rendered as a `iframe` element in the browser. This width and height is setting to iframe.

``` jsx
"use client";

import dynamic from "next/dynamic";

import PdfDocument from '@/components/pdf-document';
import React from 'react';

const PDFViewer = dynamic(() => import('@react-pdf/renderer').then(mod => mod.PDFViewer), { ssr: false });

export default function Pdf() {
    return (
        <section style={{ display: 'flex', justifyContent: 'center', alignItems: 'center', height: '100vh' }}>
            <PDFViewer width="800px" height="900px">
                <PdfDocument />
            </PDFViewer>
        </section>
    )
}
```

Let's see the screenshot. It's beautifully and easily rendered PDF document in the browser.

{% image /assets/images/react/react-pdf.png, alt="React PDF Renderer" %}

## Download the PDF Document

To download the PDF document, we can use `PDFDownloadLink` component. It allows us to download the PDF document as a file. The code is below.

``` jsx
"use client";

import dynamic from "next/dynamic";

import PdfDocument from '@/components/pdf-document';
import React from 'react';

const PDFViewer = dynamic(() => import('@react-pdf/renderer').then(mod => mod.PDFViewer), { ssr: false });
const PDFDownloadLink = dynamic(() => import('@react-pdf/renderer').then(mod => mod.PDFDownloadLink), { ssr: false });

export default function Pdf() {
    return (
        <section style={{ display: 'flex', flexDirection:'column', alignItems: 'center', height: '100vh' }}>
            <PDFViewer width="850px" height="850px">
                <PdfDocument />
            </PDFViewer>
            <PDFDownloadLink document={<PdfDocument />} fileName="my-pdf-document.pdf" style={{marginTop: '10px'}}>
                {({ blob, url, loading, error }) => (
                    loading ? 'Loading document...' : <button style={{ backgroundColor: '#171717', color: 'white', padding: '10px', borderRadius: '5px', cursor: 'pointer' }}>Download</button>
                )}
            </PDFDownloadLink>
        </section>
    )
}
```

In the above code, we have used `PDFDownloadLink` component to download the PDF document, passed the `PdfDocument` component as the `document` prop of `PDFDownloadLink` component. We have also set the `fileName` prop to `my-pdf-document.pdf` to set the file name of the downloaded file. The `style` prop is used to set the style of the download button. We also use the `loading` prop to show the loading message while the PDF document is being downloaded.

{% image /assets/images/react/react-pdf-download.png, alt="React PDF Download" %}

Below PDF document is downloaded as a file.

{% image /assets/images/react/react-pdf-doc.png, alt="React PDF Document Opened in Adobe Acrobat Reader" %}

See it's easily to create and render a PDF document in React using the `react-pdf` library.

## Conclusion
The `react-pdf` library is a React component that allows us to design and render PDF documents in React. It supports customize styles and Flex layout to create the PDF. We have learned how to create a PDF document using `react-pdf` library, how to view and download the PDF document.