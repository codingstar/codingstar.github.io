title: SCBPM paper note
date: 2015-06-08 20:15:04
tags: [学术]
---
Recently, I read a paper named <A Web Service for Scholarly Big Data Information Extraction>. Here is some notes about it.

#Abstract

### Extraction
*   The automatic extraction of metadata and other information from scholarly documents
*   In academic digital libraries, search engines, and document management systems
*   To allow for the management and categorization of documents and for search to take place

### A Web-accessible API can *simplify* this extraction
> by providing a single point of operation for extraction that can be incorporated into multiple document workflows without the need for each workflow to implement and support its own extraction functionality

CiteSeerExtractor
*   a *RESTful API* for scholarly information extraction that exploits the fact that there is duplication in scholarly big data and makes use of a near duplicate matching backend

*   the duplicate document matching results in a difference of *8.46%* in the time required to extract header and citation information from approximately *3.5 million* documents compared to a baseline.
<!--more-->
#Introduction

### Scholarly big data
> refers to the vast amount of data produced as the result of scholarly undertaking and includes journals, conference proceedings, theses, books, patents and experimental data.

### three V’s => big data
*   volume      
    Microsoft Academic contains over 50 million records for academic documents and that about 43% of the articles published between the years 2008 and 2011 are freely available online
*   velocity    
    in 2010, the annual growth rates of several popular academic databases between 1997 and 2006 ranged from 2.7 to 13.5%
*   variety
    the the different types of scholarly output that is produced

furthermore
*   value
*   veracity
*   viscosity
*   vulnerability

### services for managing and providing access to scholarly big data
*   Google Scholar
*   Microsoft Academic
*   CiteSeerχ
*   the ArXiv
    they are automatic metadata extraction.

Web-accessible API => simplify this extraction
*   provide a single point of operation that can be incorporated into multiple document and scientific workflows
*   allow for *easier processing* of data

since in scholarly publications <= duplication is common
in big data scale, methods are need to improve ectractor performance

### CiteSeerExtractor
a Web service for scholar information extraction that deals with the issue of big data by storing metadata after it is extracted

when a new paper is submitted, check if it matches with a previously submitted document

*The document matching algorithm* is able to deal with matches that are not bitwise identical and that might have minor differences.

#Related Work
### ParsCit
users submit the plain text of papers then returns the parsed citations

### GROBID
a library for extracting metadata form scholarly documents
*   header metadata, citation metadata & parse the metadata

includes a RESTful API -> access the service from other programs
*   match extracted metadata with Crossref
*   if core metadata, such as the title or first author, is matched,
*   then the system attempts to retrieve the full publisher metadata.

### FreeCite
*   based on ParsCit
*   users submit a single citation string or list of citation strings and they are parsed and tagged.

### CiteSeerExtractor
*   provides a generic framework that can easily be extended to allow for additional extractors to be incorporated (Section IV)

none of these services specifically try to address the *challenges of big data*
by making use of *near duplicate matching*

Some tools make use of Web services to perform or improve metadata extraction showing how Web services can be incorporated into metadata extraction workflows

*   PDFMeat
*   Mendeley
*   Gao's similar system

#API Design

### Resource Oriented Architecture

### CiteSeerExtractor
> a RESTful Web service based on the Resource Oriented Architecture (ROA 资源导向架构).

benefits:
*   being lightweight
*   scalable
*   easily accessible

ROA's main concepts:
*   resources:
    > something that is important enough that it is worth being referenced
*   identifiers:
    > a URI that is unique for the resource and that allows for one of the **representations of the resource** to be accessed, where a representation of a resource is some view of that resources
*   representations of resources
*   the links between resources

ROA's main properties:
*   addressability: information is exposed through URIs
*   statelessness: HTTP requests are independent of each other and can happen in isolation
*   connectedness: there are links between content
*   a uniform interface: HTTP provides a uniform interface

1. *Resources*
    > Documents (PDF, PS, TXT)
*   Submit a `POST` request to the extractor URL
*   Create a new document resource in CiteSeerExtractor
*   the text from the document is automatically extracted(PDFBox->pdf, ps2txt->ps)
*   return different status code(201 success & XML/JSON document, 503 error, over size)
        <?xml version=“1.0” encoding=“UTF-8”?>
        <CSXAPIMetadata>
        <file>base url/extractor/token/file</file>
        <header>base url/extractor/token/header</header>
        <citations>base url/extractor/token/citations</citations>
        <body>base url/extractor/token/body</body>
        <text>base url/extractor/token/text</text>
        </CSXAPIMetadata>
2. *identifiers*
    a *unique and random* identifier is assigned when a resource has successfully been created
    *   violates the ROA practice of having wellnamed resources
    *   simplifies the resource naming procedure
    *   since the resources are for the most part temporary, was considered a reasonable approach
3. *representations*
    > different views of a resource
    in CiteSeerExtractor represent different types of information extracted from the original document as well as the document itself
    --> access a resource in CiteSeerExtractor,
    an HTTP `GET` request is made to `http://$url/extractor/resource id/representation` and if success, returns an `HTTP 200 OK` status code.
    * **file**: The original document that was submitted.
    * **header**: The header of the document, including the
    title, authors, abstract, venue and any other information
    that may be extracted.
    * **citations**: The citations extracted from the document.
    * **body**: The main body text of the document, excluding
    the citations.
    * **text**: The full text of the document as extracted by an
    appropriate text extraction tool.

4. *Addressability, Statelessness, Connectedness, and Uniformity*


### HTTP Methods
Cite-SeerExtractor supports different **HTTP methods** and **output formats**(XML,JSON).
![HTTP METHODS SUPPORTED BY CITESEEREXTRACTOR](./table1.png "HTTP METHODS SUPPORTED BY CITESEEREXTRACTOR")

# Architecture
*   stand-alone
*   able to run in isolation
*   able to be integrated with a number of services

Figure 2 shows the overarching architecture of CiteSeerExtractor
![CiteSeerExtractor architecture](./figure2.png "CiteSeerExtractor architecture")

As can be seen from the figure, the RESTful API is the entry point and communicates directly with the Python Web Server, which is responsible for handling the creation of resources and for serving various representations of those resources. Security and permissions can also be controlled and implemented at the Python Web Server level.

### the RESTful API:
handle the creation of resources and serve various representations of those resources.<br/>
provides the functionality as described in Section III.

### Python Web Serer
CiteSeerExtractor is run as a stand-alone Web server and is implemented using the web.py framework.

### Extractors
1. Text Extractor: PDFBox for pdf, ps2txt for ps
2. Citation Extractor: ParsCit
3. Header Extractor: based on a tool that classifies various aspects of a header using a support vector machine
4. Body Extractor: the body of text, excluding the citations.

### File Store
Documents and their associated text representations

Access, config the permissions or delete the files on the Web server

##Duplicate Matching Backend
NoSQL backend

# Duplicate Matching Backend
The purpose of this backend is to store metadata that has already been extracted and retrieve the metadata if a document submitted is a near duplicate of a document that has previously been submitted.

the performing duplicate matching does not have a detrimental effect on the performance of the system.

### Near Duplicate Matching Algorithm
![Duplicate Matching Algorithm](./algorithm1.png "Duplicate Matching Algorithm")
