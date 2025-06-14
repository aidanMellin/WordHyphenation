# WordHyphenation
**Derived from ["Word Hy-phen-ation by Com-put-er" by Frank Liang (1983)]([https://www.tug.org/docs/liang/liang-thesis.pdf)**](https://www.tug.org/docs/liang/liang-thesis.pdf)**)  
## Summary  
  
A lightweight, fast program that can reliably hyphenate words on their syllabic breaks.  
  
**Author**: Aidan Mellin, [git@aidanMellin]([https://github.com/aidanMellin/](https://github.com/aidanMellin/))    
**Project**: [Word Hy-phen-ation]([https://github.com/aidanMellin/word-hy-phen-a-tion](https://github.com/aidanMellin/word-hy-phen-a-tion))    
**Document Created**: 2025-04-18     
  
---  
  
## Table of Contents  
  
1. [Introduction](#introduction)  
2. [Objectives](#objectives)  
3. [Motivation](#motivation)  
4. [Requirements](#requirements)  
    - Functional Requirements  
    - Non-Functional Requirements  
5. [Design and Architecture](#design-and-architecture)  
    - System Overview  
    - Detailed Design  
6. [Implementation Plan](#implementation-plan)  
    - Language and Tools  
    - Module Breakdown  
7. [Testing and Validation](#testing-and-validation)  
8. [Deployment](#deployment)  
9. [Maintenance and Future Work](#maintenance-and-future-work)  
10. [Appendices](#appendices)  
  
---  
  
## Introduction  
  
### Purpose  
  
The idea for this project first came to me in my freshman year of college, laughing at memes where someone's response or post was turned into a haiku simply because the words that they used fit the specific syllabic scheme of the poetry style. At the time I was actively developing a Discord bot that my friends and I use in the server that I own, and wanted to implement it on my own. I quickly found that there was no easy bot solution or library that I particularly wanted to throw into my code and decided that I would do it on my own.  
  
Originally I was simply using a large dictionary and key/value defined pairs to then separate all syllables in a post. I quickly found, however, that it was not only terribly optimized for storage but also categorically slow on my homebrew server hosting the bot. With this in mind, I eventually stumbled upon Frank Liang's 1983 thesis for the TeX82 systems and fell in love with it; its a lightweight, fast, and (reasonably) accurate system back geared towards considering both speed and hardware limitations in development.  
  
This project can really be considered two separate ones: the implementation of the Liang hyphenation, and the poetry "bot". The poetry will have its own extension and may ultimately warrant its own separate space for discussion, however the current plan is to implement it specifically for haikus (as Liang's returns hyphens on estimated syllabic breaks) with later extension into other poetry styles paired with rhyming and syllables.  
  
### Scope  
  
This project covers the implementation of Liang's TeX82 Hyphenation system with initial exploration into haiku recognizing as proof of concept. Limericks, cinquains, iambic pentameter will not be explored in this iteration of the project. Initial implementation will be following the outline of the thesis with heuristic optimization of pattern recognition with dabbling in extension of AI/ML concepts to increase efficiency and storage complexity.  
  
### Definitions, Acronyms, and Abbreviations  
  
#TODO: any specific terms, acronyms, or abbreviations used in the document.  
  
## Objectives  
  
### Primary Objective  
  
- Develop a lightweight, efficient algorithm for word hyphenation based on syllabic breaks.  
  
### Secondary Objectives  
  
- Ensure compatibility with multiple programming languages.  
- Enable integration with various text processing and bot frameworks.  
- Support for determining syllabic splits to aid in poetry scheme validation.  
  
## Motivation    
  
Admittedly, hyphenation is not used much today and is somewhat a lost art. Hyphenation is from a bygone era of formatting, with some even calling it ugly. The hyphenation isn't even the motivation! I would find myself laughing at the bots on Tumblr and Reddit that would derail an entire argument simply because someone typed a reply that happened to have a total of 17 syllables, so someone would "summon" the Haiku bot to then reply with the text formatted to look like a haiku.
![[Pasted image 20241013104405.png]]
[Tumblr example of Haiku Bot](https://www.reddit.com/r/tumblr/comments/12pw3ld/haiku_bot_gets_a_haiku_as_a_treat/)

- As far as I can tell, most of the haiku bots rely on a Dictionary style lookup which is good for the time part of space-time complexity, however carting around a large dictionary with the program means it's less lightweight than it can (or maybe should) be, and removes some of the fun and accuracy. The original goal of this project was to implement this program into my personal Discord bot (written in python) or for use with larger applications/bots on websites such as Reddit and Tumblr (As a note, some Haiku bots also do "guess work" in relation to counting characters which can lead to some funny interactions like )
- There are some uses of hyphenation and well-compiled and extensive dictionaries that could be used in place of this algorithm/application, but I have yet to find any that specifically implement Liang's hypothesis --and if it's good enough for TeX82 it's good enough for me-- 
  but none that can also then extend that hyphenation into poetry "solving", something that is a very entertaining concept to me.
- Developing this project in Rust will allow for ease of scalability and translation. The primary issue is that Rust is not one of my most familiar languages. Obviously I could write it in something like C++ or even C# as it is still ubiquitously used today, however in the pursuit of well-roundedness and growth I wish to learn and write this in Rust. *Note: this is subject to change as in terms of applicability Rust might not be the best solution*
## Requirements
### Functional Requirements  
  
- The program must hyphenate words based on syllabic breaks.  
- The system should provide an API for integration with other programming languages. (E.g. Libraries for Python using PyO3 and .dll linkage with C++) 
- The tool should support syllabic analysis for poetry schemes like haikus.  
  
### Non-Functional Requirements  
  
- The solution should be lightweight and performant.  
- It must be easily maintainable and extensible.  
- The program should be well-documented and user-friendly.
  
## Design and Architecture  
### System Overview  
  
- General system design
	- The application or use of pattern generation
	- The training of the patterns
- #TODO: any diagrams that might be helpful
  
### Detailed Design  
  
For this project, the Liang Hyphenation System can be split into two primary sections:  
- #### The Dictionary  
	- To get the hyphenation patterns (the actual driving force of determining the hyphenation points), we need a list of words to test it on.  
	- Getting the words is fairly easy, but the storage and accessing it (finding the middle ground between time complexity for accessing and storage size), so the solution Liang found was using a packed (indexed) trie.  
	- Tries are fairly intuitive. Instead of having a large list of all the words that you might want to use, you can simply use a tree to separate by letter and then scan through that for use. While fast, the issue is that this can fairly quickly grow large.  
	- **Enter indexed tries:**
		- Indexed tries use a large array to store pointers for each of the children contained in the array. You have to intialize a super large array (26<sup>2</sup>) to be precise. The issue with this is that not all of these spaces allocated in the array are actually going to be used. It's a lot of wasted space. All of those null pointers and nulls don't need to be there.    
	-  So, **Packed tries**.    
		- Packed tries are essentially indexed tries, but just... packed tighter.  
		- You loop through the trie initially created, and simply go level by level putting in pointers in a first fit method, where there is no overlap of the pointers themselves, or where they're pointing to. But what do I mean by that? I probably won't do Liang's explanation justice, but essentially our packed trie is going to store two items per index in the array: the letter it corresponds to, and the pointer to the next related letter.    
		- All letters are assigned their own index (think offset) such that if we wanted to find HAVE (the example provided in the thesis) we would first look for H with the provided offset (8), and see that in that index, there is both an H and 30.    
		- We travel to 30, and know that the next letter we are looking for is an A, so we add one (the offset of A in the alphabet) to the index of 30, and find our A with a provided pointer to 29. (Alternatively if we wanted to find HE, we would go to 30, add 5, and in index 35 there exists an E that can function as a terminal state).    
		- We go to 29, and indexing by V gets us a 51, this points to 2.    
		- In location 7 (2, the given index plus 5 -- offset of E), we find E that coincidentally points to 0, which means there are no possible children states.  
		![[Pasted image 20241013103844.png]]
		_("Word Hy-phen-a-tion by Com-put-er", Liang page 16)_  
- #### The Patterns  
	- #TODO test space-time complexity of the various storage methods and display metrics  
	- The (hyphenation) patterns are the key to this whole project. Instead of just having a large list of what words need or can be hyphenated, we want to algorithmically establish what kind of rules we have in place to then be able to hyphenate words.  
	- Part of the issue with this is that there are a lot of words. Like a lot. Obviously there will be some overlap with patterns, but there are also going to be a number that are unique and as such can bloat the time complexity, especially as these words are often not very popularly used. This will likely result in pruning based off of word frequency usage related to the end destination of the libary/program, more covered in [Space Time Complexity](#space-time-complexity)
- **Error handling**
	- Admittedly, there won't be much of an issue with errors so long as basic text conversion is applied. All tries, tables, and patterns will be precomputed prior to any kind of interfacing the library may do with the library itself, so most errors will likely be edge cases
- **Edge Cases**
	- There will likely be edge cases. The primary pipeline of the project though is to first search for predefined instances in the pattern table and if that doesn't exist, cobbling together a predicitve hyphenation pattern from the available sources.
	- While 

### Space Time Complexity
- One of the concerns for this project (besides it functioning) is the space-time complexity. This application needs to be lightweight and fast
- #### Lightweight Concerns and solutions
	- Dictionaries
	- Packaging Dictionaries for specific uses (Discord/Reddit/specific SubReddit, etc)
- #### Speed Concerns and solutions
	- Trie lookup on a mass scale
	- Hybridization of lookup protocols (only if frequency table is established for specific distributions of libraries)
  
## Implementation Plan  
  
### Language and Tools  
  
- Overview of Rust and its suitability for this project.  
- List of libraries, frameworks, and tools that will be used.  
  
### Module Breakdown  
  
- Description of the main modules and their responsibilities.  
    - Hyphenation Engine  
    - Syllable Detector  
    - API Layer  
    - Integration Modules  
  
## Testing and Validation  
  
### Testing Strategy  
  
- Unit tests for individual components.  
- Integration tests for overall system functionality.  
- Performance tests to ensure efficiency.  
- Utilize the Red/Green/Refactor method to ensure mass coverage of potential test cases  
  
### Validation  
  
- Methods to validate the correctness of the hyphenation and syllable detection.  
- Testing file to be randomly tested against during heuristics and final testing stages. Then a separate file full of new words  
  
## Deployment  
  
### Deployment Strategy  
  
- Steps to build and package the application.  
- Guidelines for deploying in various environments.  
  
### Documentation  
  
- User guide for installation and usage.  
- Developer guide for contributing to the project.  
  
## Maintenance and Future Work  
  
### Maintenance  
  
- Plans for regular updates and bug fixes through Github user issue reporting  
- This project will likely not be big enough to require open-sourcing or multiple contributors, however should that stage be reached steps will be taken from there  
  
### Future Work  
  
- Potential features and improvements.  
- *Long Term Vision*
	- The primary purpose of this application is for me to learn more about algorithm implementation, Rust, and full application building/planning. 
	- Where it would be cool to see this app go would be to have it packaged in such a way that it is light and cheap enough to be able to run on a large scale messaging service (such as one subreddit) to be able to read all messages and replies on the forum and reply to any that fit the criteria of a poem.
	- Lastly it would be awesome to have this bot hosted in an entirely cloud environment, independent of any specific server (personal laptop) for hosting.
  
## Appendices  
  
### References  
  
  
### Glossary  
  
- #TODO
  
### Acknowledgments  
  
 - Frank Liang (Obviously)
