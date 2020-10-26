# CalDART Web Proposal

This document proposes an appropach to improving the perormance and maintainability of the CalDART.org web site.


## Current Site

The current CalDART site is built with [WordPress](https://wordpress.org/) and the [CiviCRM](https://civicrm.org/home) platform. Some deficiencies with the current implementation include:
 * The CiviCRM / WordPress implementation requires a server that requires regular updates.
 * The page layout is not "responsive" so it doesn't adapt well to mobile devices.
 * Page generation and delivery is slow. 

## Definitions

 - Responsive Design
 - Static Site Generator
 - Source Code Control / Git
 - Continuous Integration / Continuous Delivery
 - Object Storage
 - Content Delivery Network
 - Serverless

## Proposed Changes

The changes proposed include using a static site generator with responsive HTML templates and a content delivery network \(CDN\).

A [Static Site Generator](https://gohugo.io/about/benefits/) merges content with templates to generate static HTML files that are deployed to a hosting provider. These files only need to be generated once each time content is published to the site. Unlike WordPress, where the content is generated each time a page is requested.

There are [many site generators](https://jamstack.org/generators/) available. The author has the most experience with [Hugo](https://gohugo.io) so that is the system being proposed. In addition to the Site Generator, services are needed for source control, deployment automation, and content delivery.

[GitHub](https://github.com/) is a service that provides hosted [Git](https://git-scm.com/) version control services. The content and templates for the CalDART site will be stored in the version control system.

As content is added to the Git repository, it can be reviewed through a system of [Pull Requests](https://docs.github.com/en/free-pro-team@latest/github/collaborating-with-issues-and-pull-requests/about-pull-requests).  Once a Pull Request is approved, automated tools will process the content through Hugo and store the generated pages.  

The Hugo automation can be run through [GitHub Actions](https://github.com/features/actions) or Amazon Web Service's (AWS) [CodePipeline](https://aws.amazon.com/codepipeline/).  Either of these tools, will be triggered by changes in the GitHub content, process the changes, and store the generated content or send a notification if there was an error.

The generated content can be stored with a number of services but one of the most cost effective and reliable services is the AWS [Simple Storage Service \(S3\)](https://aws.amazon.com/s3/).  S3 also provides full integration with the AWS [CloudFront](https://aws.amazon.com/cloudfront/) service.  CloudFront is th recommended CDN.  The CDN improves content delivery performance by serving the static web pages from a network of servers that cache content.

