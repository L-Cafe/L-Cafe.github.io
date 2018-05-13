---
layout: post
title: "Don't share Microsoft Office documents: use open formats"
date: "2017-03-21 18:59:32 +0100"
author: L1Cafe
tags: FOSS rant opinion
---


It's very common, in certain situations, to share OOXML files such as .docx from Microsoft Word and .pptx from Microsoft PowerPoint. I've seen this happen most often in schools, universities, and educational institutions in general. I will try to explain why this is generally a bad idea.

It's not the first, and probably won't be the last time, people complain about OOXML, Microsoft Office, and their dirty tricks. Here's a few references in no particular order: [consortiuminfo.org](http://www.consortiuminfo.org/standardsblog/article.php?story=20070117145745854), [GNU](https://www.gnu.org/philosophy/no-word-attachments.en.html), [FSF](https://www.fsf.org/campaigns/opendocument/reject), [stephesblog.blogs.com](http://stephesblog.blogs.com/my_weblog/2007/08/microsofts-fail.html), [Ars Technica](https://arstechnica.com/information-technology/2008/02/ibm-responds-to-microsoft-ooxml-is-technically-inferior/), [GoldMark](http://www.goldmark.org/netrants/no-word/attach.html), [Tristan Miller](https://logological.org/word)... The list could go on and on forever, but I think the links provided make valid points. Now It's my turn:

# Its formatting is not consistent

Microsoft's own OOXML format isn't designed to be consistent across all platforms and applications. While it definitely is an open format, some critics claim that its standard specification is too long to be implemented realistically. Moreover, even Microsoft doesn't respect its own standard in a consistent manner across their Microsoft Office releases. This means that a document produced with Microsoft Word 2007 is likely to have inconsistencies when opened in a newer version such as Microsoft Word 2016. The truth is that, while Microsoft tries to insert features in newer Office releases that maintain a certain degree of retrocompatibility, this often introduces an inferior solution in the form of a workaround. What's worse, this situation, when handled by a third party such as Apple (With Apple Pages, for example), or free software office suites such as LibreOffice, will increase the cost of maintenance, and it's very likely the workaround will not be as effective as Microsoft's implementation for practical reasons.

# Third party support isn't great

Related to the above point, to correctly display OOXML files you're going to need the latest version of Microsoft Office (which isn't free as in beer or as in freedom), or one of the Office documents viewing tools Microsoft provides at no extra charge (this is only for Windows though, macOS users are left in the dark). If you have ever tried to view a Microsoft Word-produced document in Apple Pages or LibreOffice Writer, you probably know it will not be pixel-perfect (some images might be offset, text fonts might not display correctly unless you have the same font set, margins and some special formatting could be configured incorrectly, etc). This also happens vice versa: Apple Pages-produced OOXML documents opened in Microsoft Word, or even LibreOffice Writer. However, PDF (Portable Document Format) is a fully open specification, it's universal, and can be viewed in any modern operating system without additional software (even modern browsers ship with PDF viewers built in).

# Its openness is debatable

While the format itself is an open specification, Microsoft has a long record of using the [EEE](https://en.wikipedia.org/wiki/Embrace%2C_extend_and_extinguish) strategy to marginalise and neutralise competition. This means that, while they might release something that looks like an open standard anyone can adopt, the truth is that OOXML is [too hard to implement, even for Microsoft](http://www.sutor.com/newsite/blog-open/?p=2069). While other office suites can adopt OOXML formats, Microsoft Office can always add new features that break compatibility with these office suites that don't implement them.

# They can contain malware in the form of macros

This is a well known fact by malware designers, information security professionals, and most software engineers. This is such a commonly known fact that Microsoft Office, by default, opens unknown origin documents in read-only mode, disabling macros, and warns users in multiple ways of allowing edits on these documents. Warnings that are, by the way, typically dismissed by inexperienced users.

# Editing shared documents is often unnecessary

When sharing an OOXML document, the receiver will have a file they can easily edit or modify. Therefore, when attaching invoices, or finished works, if you use the PDF format, the document will be displayed exactly the way you want it to be. Microsoft Office supports exporting to PDF in its latest versions, as well as LibreOffice and essentially most office suites, including Apple's and Google's.

# It's a poor choice for forms

People sometimes use Office documents to send blank forms to be filled in by the recipient. This document passing forth and back is unnecessary, slow, and inconvenient. Where you could simply present a webpage with a form, and have a web server receive all that data in a manner that can be automatically stored into databases for further processing, encrypted for security, or analysed, the choice of using a document format that cannot facilitate automation seems rather an uninformed and suboptimal decision.

# Microsoft Office has terrible formula support

When producing documents with math and physics formulas, Microsoft Office has a tool for inserting equations, however, this tool, while easy to use for sporadic situations, becomes a nightmare in terms of UX, because it's too difficult to insert reasonably complex formulas using Microsoft Office's system. The most reasonable option, and what most math university teachers and doctors use is LaTeX. LaTeX typically produces PDF documents, and has a very solid syntax, built on top of TeX. Both are open source typesetting systems that produce beautiful, solid, and consistent output and it's the desirable choice for productivity. Here's some more information on Word vs LaTeX: [MIT](http://web.mit.edu/klund/www/urk/texvword.html), [ResearchGate](https://www.researchgate.net/post/Why_LaTex_is_better_choice_than_Microsoft_Word),  [StackExchange](https://tex.stackexchange.com/questions/1756/why-should-i-use-latex), [Reddit](https://www.reddit.com/r/LaTeX/comments/26z3ii/why_is_latex_better_than_the_current_version_of/), [Andrew Roberts](http://www.andy-roberts.net/writing/latex/benefits.

