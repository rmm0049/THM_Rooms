# What's the worst that could happen?
### Story
McPayroll is processing the bonuses for all the hardworking elves. One of the Elves has sent McPayroll a file that they're claiming contains their updated payment information. The only problem is that she doesn't recognize the Elf - could this be a sneaky attack from Grinch Enterprises to cause more havoc? Analyze the file to see if you can determine whether it's malicious or not!

McPayroll provides this file to McSkidy who is a member of the security team. McSkidy is a wise elf. She knows to analyze this file in a sandboxed environment. Therefore, she fired up her [Remnux](https://remnux.org/) VM to take a look at the suspicious file. She unzipped the archive and saw that there was a folder named 'Samples' and a file named 'testfile' in the zip archive. She copied these files over to her Remnux desktop to begin the analysis.

Learning Objectives:

In this task, we will learn:

1.  How to identify the file type of a file regardless of file extension
2.  How to find strings in a file
3.  How to calculate hash of a file
4.  Using VirusTotal to perform preliminary analysis of a suspicious file

### Initial Analysis
To get started with the analysis, start running the `file` command. This is a Linux utility that helps determine a given file's file type, regardless of the file extension that it may have.

Start with:
`file <filename>`