# README 101




## What is it?
A [README](https://en.wikipedia.org/wiki/README) is a text file that introduces and explains a project. It contains information that is commonly required to understand what the project is about.



## Why should I make it?
It's an easy way to answer questions that your audience will likely have regarding how to install and use your project and also how to collaborate with you.



## Who should make it?
Anyone who is working on a programming project, especially if you want others to use it or contribute.



## When should I make it?
Definitely before you show a project to other people or make it public. You might want to get into the habit of making it the [first file you create](https://tom.preston-werner.com/2010/08/23/readme-driven-development.html) in a new project.



## Where should I put it?
In the top level directory of the project. This is where someone who is new to your project will start out. Code hosting services such as [GitHub](https://github.com/), [Bitbucket](https://bitbucket.org/), and [GitLab](https://about.gitlab.com/) will also look for your README and display it along with the list of files and directories in your project.



## How should I make it?
While READMEs can be written in any text file format, the most common one that is used nowadays is [Markdown](https://en.wikipedia.org/wiki/Markdown). It allows you to add some lightweight formatting. You can learn more about it at the [CommonMark website](https://commonmark.org/), which also has a helpful [reference guide](https://commonmark.org/help/) and an [interactive tutorial](https://commonmark.org/help/tutorial/).

Some other formats that you might see are [plain text](https://en.wikipedia.org/wiki/Text_file), [reStructuredText](https://en.wikipedia.org/wiki/ReStructuredText) (common in [Python](https://www.python.org/) projects), and [Textile](https://en.wikipedia.org/wiki/Textile_(markup_language)).

You can use any text editor. There are plugins for many editors (e.g. [Atom](https://github.com/atom/markdown-preview), [Emacs](https://github.com/jrblevin/markdown-mode), [Sublime Text](https://github.com/revolunet/sublimetext-markdown-preview), [Vim](https://github.com/suan/vim-instant-markdown), and [Visual Studio Code](https://code.visualstudio.com/docs/languages/markdown#_markdown-preview)) that allow you to preview Markdown while you are editing it.

You can also use a dedicated Markdown editor like [Typora](https://typora.io/) or an online one like [StackEdit](https://stackedit.io/app) or [Dillinger](http://dillinger.io/). You can even use the editable template below.





# Suggestions for a good README
Every project is different, so consider which of these sections apply to yours. The sections used in the template are suggestions for most open source projects. Also keep in mind that while a README can be too long and detailed, too long is better than too short. If you think your README is too long, consider utilizing [another form of documentation](https://www.makeareadme.com/#more-documentation) rather than cutting out information.



## Name
Choose a self-explaining name for your project.



## Description
Let people know what your project can do specifically. Provide context and add a link to any reference visitors might be unfamiliar with. A list of **Features** or a **Background** subsection can also be added here. If there are alternatives to your project, this is a good place to list differentiating factors.



## Badges
On some READMEs, you may see small images that convey metadata, such as whether or not all the tests are passing for the project. You can use [Shields](http://shields.io/) to add some to your README. Many services also have instructions for adding a badge.



## Visuals
Depending on what you are making, it can be a good idea to include screenshots or even a video (you'll frequently see GIFs rather than actual videos). Tools like [ttygif](https://github.com/icholy/ttygif) can help, but check out [Asciinema](https://asciinema.org/) for a more sophisticated method.



## Installation
Within a particular ecosystem, there may be a common way of installing things, such as using [Yarn](https://yarnpkg.com/), [NuGet](https://www.nuget.org/), or [Homebrew](https://brew.sh/). However, consider the possibility that whoever is reading your README is a novice and would like more guidance. Listing specific steps helps remove ambiguity and gets people to using your project as quickly as possible. If it only runs in a specific context like a particular programming language version or operating system or has dependencies that have to be installed manually, also add a **Requirements** subsection.



## Usage
Use examples liberally, and show the expected output if you can. It's helpful to have inline the smallest example of usage that you can demonstrate, while providing links to more sophisticated examples if they are too long to reasonably include in the README.



## Support
Tell people where they can go to for help. It can be any combination of an issue tracker, a chat room, an email address, etc.



## Roadmap
If you have ideas for releases in the future, it is a good idea to list them in the README.



## Contributing
State if you are open to contributions and what your requirements are for accepting them.

For people who want to make changes to your project, it's helpful to have some documentation on how to get started. Perhaps there is a script that they should run or some environment variables that they need to set. Make these steps explicit. These instructions could also be useful to your future self.

You can also document commands to [lint the code](https://stackoverflow.com/questions/8503559/what-is-linting) or [run tests](https://en.wikipedia.org/wiki/Test_automation). These steps help to ensure high code quality and reduce the likelihood that the changes inadvertently break something. Having instructions for running tests is especially helpful if it requires external setup, such as starting a [Selenium](http://www.seleniumhq.org/) server for testing in a browser.



## Authors and acknowledgment
Show your appreciation to those who have contributed to the project.



## License
For open source projects, say how it is [licensed](https://www.makeareadme.com/#license-1).



## Project status
If you have run out of energy or time for your project, put a note at the top of the README saying that development has slowed down or stopped completely. Someone may choose to fork your project or volunteer to step in as a maintainer or owner, allowing your project to keep going. You can also make an explicit request for maintainers.