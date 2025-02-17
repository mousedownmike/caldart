# CalDART Web Upgrade Proposal

This document proposes an upgrade of the CalDART.org web site to improve performance, usability, and reduce maintenance overhead.

## Current Site

The current CalDART site is built with [WordPress][1] and the [CiviCRM][2] platform. From an outsider's view the current system seems to suffer from the following issues:

-   The CiviCRM / WordPress implementation requires a server that requires regular updates.
-   The page layout is not "responsive" so it doesn't adapt well to mobile devices.
-   Page generation and delivery can be slow (4-5 seconds).

The system proposed in this document aims to resolve these and other issues.

## Definitions

The proposed solution involves several technical components that require some definition. The purpose of this section is to provide general definitions and explanations for the terms and technologies that will be elaborated on in the proposal section.

-   **Responsive Design** - Responsive Design generally refers to designing web pages in a way that permits page code to "respond" or change to the type of device where the page is rendered. With Responsive Design, you create a single HTML page and set of Cascading Style Sheets (CSS) for each page "type" you have. Depending on the type of page and the device, the layout of the content will change. This eliminates the need to manage separate content and designs for each supported device type.
-   **Scalable Vector Graphics** - Scalable Vector Graphics (SVG) are images that can be drawn by browsers using vector commands (like strokes of a brush) as opposed to the placement of pixels. SVG assets can scale up and down to appropriately fit the device where they are drawn.
-   **Static Site Generator** - A Static Site Generator is a tool that combines content and styles into a set of static files that are served directly to viewers without further intervention. Unlike WordPress, a Static Site Generator is not involved in satisfying every request. Pages are generated when content changes and then published to a location where they are served directly to viewers.
-   **Markdown** - Markdown is a markup language that provides a simplified means of formatting content. Static Site Generators often use Markdown as the format for content.
-   **Git** - Git is a popular version control system that is used by software developers to track and manage changes to source code. [GitHub][3] is a popular service owned by Microsoft that provides a free hosted version of Git. Git has become increasingly popular outside of software development to encompass many use cases where versioned content and collaboration is important. GitHub has built a set of tools and services on top of Git to support work planning, issue tracking, and approval processes.
-   **Pull Request** - A Pull Request is a workflow that can be used for approving changes in a Git repository. Changes are made in one branch of the content and a request is made to have those changes pulled into the primary branch. This process can be regimented and require multiple distinct users or it can all be performed by the same user.
-   **Continuous Delivery** - Continuous Delivery is the concept of continuously delivering updates as and when they are made. The idea is to see changes as they are made as opposed to making days, weeks, even months of changes before seeing the results. This is important in software development but the same tools can also be used in content management. In a Continuous Delivery workflow, as changes are made (committed) to a Git repository, tools identify the changes, generate the necessary content and deliver the changed content to an appropriate environment (i.e. a preview environment before a production deployment).
-   **Object Store** - An Object Store is a common way to store data in "the cloud". Data stored in an Object Store is secure, durable (i.e. unlikely to be lost), and highly available (i.e. quick to retrieve). An Object Store simplifies and reduces the cost of managing data. The provider of the Object Store takes on the responsibilities of backing up data and making sure the available storage space never runs.
-   **Content Delivery Network** - A Content Delivery Network (CDN) is a collection of geographically distributed servers that speed up the delivery of content to viewers by caching copies of content close to where the content will be consumed. When a request is made for content, it is routed to the nearest CDN server. If the server has a cached copy of the requested content, that content is immediately returned. If the content is not available locally, the CDN server retrieves the content from the "origin" server (possibly an Object Store), delivers it to the viewer, and stores a copy for future requests.
-   **Serverless** - Serverless is a common marketing term that refers to a service where the provider manages all of the server resources and customers provide a single function that they want to run. Customers only pay for the time and resources used when their function is executing. This can significantly reduce operating costs by having the provider manage software and security updates and by only paying for server resources when they're being used.
-   **Infrastructure as Code** - Infrastructure as Code is a methodology where infrastructure changes (e.g. databases, servers, domains, etc...) are managed through configuration files that are tracked for changes. This reduces the problems encountered when a change is made in an administrative console and subsequently forgotten. New environments and systems can be brought up with the execution of a single command as opposed to following a multi-page document of error-prone instructions.

The next section describes how these terms can come together to provide an improved CalDART.org experience.

## Proposal

This proposal includes updates to the site design, content development, and content delivery. This document does not propose changing the subscription and renewal services until the performance and usability of the main site are addressed.

### Site Design

The current site is laid out with a header, content area, and footer. The header is comprised of a set of logos and a primary navigation bar. The content area contains each page's content and a side-bar that appears to be used only by a search field. Except for a link to the site's theme creator, the footer is unused.

The general layout and site organization will be maintained but simplified with the following design changes:

-   Logos will be regenerated using SVG to support scaling.
-   Non CalDART Logos (DART, CalPilots) will be moved to the footer.
-   Infrequently used navigation will be moved to the footer.
-   The header navigation will transition to a collapsible menu when viewed on smaller screens.
-   The search field will be removed allowing the main content to consume the full width of the page.
-   Fonts, spacing, and colors will be updated to conform to content accessibility guidelines.
-   The footer will be updated to contain useful links and attributions (e.g. Cal Pilots).

The general appearance of the site should be reminiscent of the current design but the fundamental page templates will be completely new. These new templates will follow modern guidelines for building responsive and accessible sites.

### Content Development

The bulk of the work involved in this proposal is concerned with how content is developed and delivered to viewers. The basic content development workflow is:

1.  A content editor makes changes to files and reviews those changes on their computer using a local copy of the static site generator.
2.  When the changes are ready to be deployed, they are committed to a Git repository and a Pull Request is made.
3.  The Pull Request triggers a Continuous Delivery action to generate a new version of the site that is deployed to a preview location.
4.  When the Pull Request is approved, the content is merged into the primary branch. This triggers a Continuous Delivery action to regenerate the site and save the generated files in an Object Store.

The proposed tools for this workflow are:

-   [Hugo][4] - Hugo is a free and Open Source Static Site Generator that supports local development, custom templates, and fast content generation.
-   [GitHub][5] - GitHub has become the leader in Git repository hosting. It is owned by Microsoft and provides free tiers that will likely satisfy all of the requirements for the CalDART Git repository needs. In addition to Git hosting, [GitHub Actions][6] may satisfy the needs of the Continous Delivery tools.
-   [AWS CodePipeline][7] - CodePipeline is the "pay as you go" Continuous Delivery service provided by Amazon Web Services. Depending on costs and integration requirements, CodePipeline may be used as the Continuous Delivery tool.
-   [AWS Simple Storage Service (S3)][8] - S3 is the industry-standard object storage service. It will provide cost-effective, reliable storage for the generated CalDART site.

### Content Delivery

To improve performance, security, and reliability, content stored in the Object Store will be delivered through a Content Delivery Network. With a CDN in place, a page request is satisfied with the following flow:

1.  A browser requests a caldart.org resource (i.e. an HTML page, image, etc...).
2.  When the browser retrieves the internet address for caldart.org, it receives the address of the nearest available node in the CDN.
3.  The resource request is communicated to the CDN node.
4.  The CDN node looks for the resource in its local cache. If the resource is found, it is immediately returned to the browser.
5.  If the CDN does not find the resources in its local cache, it retrieves the resource from the Object Store.
6.  When the CDN receives the resource from the Object Store, it returns it to the browser and stores a local copy that it can use for future requests.

The proposed tools for this workflow are:

-   [AWS Route 53][9] - Route 53 is a Domain Name System (DNS) service provided by Amazon Web Services. DNS is the service that allows computers to resolve network addresses from named addresses like caldart.org. The CDN must be fully integrated with the DNS service so the address of an appropriate CDN node can be used.
-   [AWS CloudFront][10] - CloudFront is a high-performance Content Delivery Network operated by Amazon Web Services. When integrated with other AWS solutions (S3, Route 53, and others), CloudFront is a cost-effective and secure CDN solution.

### Infrastructue as Code

As proposed, the system relies heavily on existing software and services maintained by third parties such as Microsoft and Amazon. This dramatically reduces the maintenance overhead, improves reliability, and is likely to reduce operating costs. However, managing the configuration of the various services can be complicated, error-prone, and difficult to track if done manually. This proposal calls for the use of Infrastructure as Code tools that manage these services in a well documented, repeatable fashion across providers.

The proposed Infrastructure as Code tool is:

-   [Terraform][11] - Terraform by Hashicorp is a leading open-source tool that supports Infrastructure as Code processes. For this proposal, it will be used to manage GitHub and AWS resources

## Future Work

This proposal does not account for migrating the membership renewal process to new systems. Without greater visibility into the workflows, data, and systems involved, it is difficult to propose a viable replacement. However, the use of Stripe as a payment processor helps reduce the complexity of integration and it is hoped that the existing system could be temporarily placed in the context of the new site (i.e. a new window or linked to from the new site). As more information becomes available, a more comprehensive membership management system could be developed.

## About The Author

My name is Mike Dalrymple and I'm a software engineer based in San Luis Obispo. I've been developing web based software for over 20 years.  My most recent company was sold to Comcast in 2016 and I've been working on a new General Aviation focued project for the past two years.  I have a fair bit of experience with the technologies mentioned in this proposal.  My personal site [mikedalrymple.com][12] is built with most of the proposed tools and should provide a great starting point for this project.

[1]: https://wordpress.org/
[2]: https://civicrm.org/home
[3]: https://github.com
[4]: https://gohugo.io
[5]: https://github.com
[6]: https://github.com/features/actions
[7]: https://aws.amazon.com/codepipeline/
[8]: https://aws.amazon.com/s3/
[9]: https://aws.amazon.com/route53/
[10]: https://aws.amazon.com/cloudfront/
[11]: https://www.terraform.io
[12]: https://mikedalrymple.com