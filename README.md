# CalDART Web Proposal

This document proposes an appropach to improving the perormance and maintainability of the CalDART.org web site.


## Current Site

The current CalDART site is built with [WordPress](https://wordpress.org/) and the [CiviCRM](https://civicrm.org/home) platform. Some deficiencies with the current implementation include:
 * The CiviCRM / WordPress implementation requires a server that requires regular updates.
 * The page layout is not "responsive" so it doesn't adapt well to mobile devices.
 * Page generation and delivery can be slow (4-5 seconds). 

## Definitions

The proposed solution involves a number of technical components that require some definition.  The purpose of this section is to provide general definitions and explanations for the terms and techologies that will be elaborated on in the proposal section.   

 - **Responsive Design** - Responsive Design generally refers to designing web pages in a way that permits page code to "respond" or change to the type of device the page is being rendered on.  With Responsive Design, you create a single HTML page and set of Cascading Style Sheets (CSS) for each page "type" you have.  Depending on the type of page and the device, the layout of the content will change.  This eliminate the need to manage separate content and designs for each supported device type.
 - **Static Site Generator** - A Static Site Generator is a tool that combines content and styles into a set of static files that are served directly to viewers without further intervention.  Unlike WordPress, a Static Site Generator is not involved in satisfying every request.  Pages are generated when content changes and then published to a location where they are served directly to viewers.
 - **Git** - Git is a popular version control system that is used by software developers to track and manage changes to source code.  [GitHub](https://github.com) is a popular service owned by Microsoft that provides a free hosted version of Git. Git has become increasingly popular outside of software development to encompass many use cases where versioned content and collaboration is important.  GitHub has built a set of tools and services on top of Git to support work planning, issue tracking and approval processes. 
 - **Continuous Delivery** - Continuous Delivery is the concept of continuously delivering updates as and when they are made.  The idea is to see changes as they are made as opposed to making days, weeks, even months of changes before seeing the results.  This is important in software development but the same tools can also be used in content management.  In a Continuous Delivery workflow, as changes are made (committed) to a Git repository, tools identify the changes, generate the necessary content and deliver the changed content to an appropriate environment (i.e. a preview environment before a production deployment).
 - **Object Store** - An Object Store is a common way to store data in "the cloud". Data stored in an Object Store is secure, durable \(i.e. unlikely to be lost\) and highly available \(i.e. quick to retrieve\). An Object Store simplifies and reduces the cost of managing data.  The provider of the Object Store takes on the responsibilities of backing up data and making sure the available storage space never runs.
 - **Content Delivery Network** - A Content Delivery Network \(CDN\) is a collection of geographically distributed servers that speed up the delivery of content to viewers by caching copies of content close to where the content will be consumed.  When a request is made for content, it is routed to the nearest CDN server.  If the server has a cached copy of the requested content, that content is immediately returned.  If the content is not available locally, the CDN server retrieves the content from the "origin" server \(possibly an Object Store\), delivers it to the viewer and stores a copy for future requests.
 - **Serverless** - Serverless is a common marketing term that refers to a service where the provider manages all of the server resources and customers provide a single function that that want to run.  Customers only pay for the time and resources used when their function is executing.  This can significantly reduce operating costs by having the provider manage software and security updates and by only paying for server resources when they're actually being used. 
 - **Infrsastructure as Code** - Infrastructure as Code is a methodology where changes to infrastructure \(e.g. databases, servers, domains, etc...\) are managed through configuration files that are tracked for changes.  This reduces the problems encountered when a change is made in an administrative console and subsequently forgotten.  New environments and systems can be brought up with the execution of a single command as opposed to following a multi-page document of error-prone instructions.

 The next section describes how these terms can come together to provide an improved exprience with CalDART.org.

## Proposal

The changes proposed include using a static site generator with responsive HTML templates and a content delivery network \(CDN\).

A [Static Site Generator](https://gohugo.io/about/benefits/) merges content with templates to generate static HTML files that are deployed to a hosting provider. These files only need to be generated once each time content is published to the site. Unlike WordPress, where the content is generated each time a page is requested.

There are [many site generators](https://jamstack.org/generators/) available. The author has the most experience with [Hugo](https://gohugo.io) so that is the system being proposed. In addition to the Site Generator, services are needed for source control, deployment automation, and content delivery.

[GitHub](https://github.com/) is a service that provides hosted [Git](https://git-scm.com/) version control services. The content and templates for the CalDART site will be stored in the version control system.

As content is added to the Git repository, it can be reviewed through a system of [Pull Requests](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/about-pull-requests).  Once a Pull Request is approved, automated tools will process the content through Hugo and store the generated pages.  

The Hugo automation can be run through [GitHub Actions](https://github.com/features/actions) or Amazon Web Service's (AWS) [CodePipeline](https://aws.amazon.com/codepipeline/).  Either of these tools, will be triggered by changes in the GitHub content, process the changes, and store the generated content or send a notification if there was an error.

The generated content can be stored with a number of services but one of the most cost effective and reliable services is the AWS [Simple Storage Service \(S3\)](https://aws.amazon.com/s3/).  S3 also provides full integration with the AWS [CloudFront](https://aws.amazon.com/cloudfront/) service.  CloudFront is th recommended CDN.  The CDN improves content delivery performance by serving the static web pages from a network of servers that cache content.

