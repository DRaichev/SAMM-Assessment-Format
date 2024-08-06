# Rationale
I propose the extension .samm   
The easiest way to represent an assessment is as a JSON file.
On top of that we can define the structure of the file.

## Representing an answer
A minimal example representation of an answer could be:
**{questionId} : {answerOrder}**

```JS
"8fd0374f0b63476eacb3cadca99b1538" : 3
```

An alternative approach, that is still compact, but more human friendly is to use the question code (BusinessFunction-Practice-Stream-Level), and answer value (score) as follows:

```JS
{
  "questionCode": "G-SM-A-1",
  "answerScore": 1.00,
}
```

Lets call the snippet above a SAMM Answer

Additional (redundant) info that could be useful in making the output more human-readable would be the question text and answer text.

Other possible candidates might be questionId, and answerOrder, but they are redundant to a machine parser, and unhelpful for a human reader, so they are best omitted.

I believe the lean representation is better, so I will stick with it, and will propose a framework for easy extension of the .samm format with verbose data, such as question and answer text, if it is ever needed.

## Representing an assessment
An assessment is essentially an array of SAMM Answers
```JS
[
  {...}, //SAMM Answer
  ...
  // 90 SAMM Answers in total
]
```

But there is some more metadata that will be useful - SAMM version, Organisation Name, Scope Name, Date

So we end up with a format that would look something like this:

```JS
{
  "version": "2.0.8",
  "organisation": "Acme Corporation",
  "scope": "Team Coyote",
  "date": "2024-07-04",
  "answers": [ {...} ... ] // 90 SAMM Answers in total
}
```

What we have above is a minimal SAMM assessment representation.
*Note: The organisation and scope can be blank*


The .samm format should be pretty stable in its structure, but it might change in the future. For this purpose I propose the following metadata be added:
formatVersion: the version of the samm format that is used

```JS
{
  "formatVersion": "1.0.0",
  "assessment": {
    "version": "2.0.8",
    "organisation": "Acme Corporation",
    "scope": "Team Coyote",
    "date": "2024-07-04",
    "answers": [ {...} ... ] // 90 SAMM Answers in total
  }
}
```


The format should also be extensible, but extension formats should be backward compatible with the core .samm format, i.e. readable by .samm readers. For this purpose I propose the core and extension parts be split in separate sections.

An example extension would be the Verbose JSON Answers, or the benchmark format, which has a few additional fields like company industry, number of devs, security champions and app sec personnel, geographic region, etc.

Extensions can add whatever data they want to, but they should have the following fields:  
name: the name of the extension  
version: the version of the extension  
Everything else is up to the extension owner.

This will allow for easy extension, while keeping the extended formats backward compatible. It will also allow the use of more than one extension in a single .samm file. The only caveat is the possibility of more than one instance of the same extension being added to the same file. The responsibility to detect and handle this is left to the users. It will not affect the core .samm readers in any way, so it is not a major concern.


Here is an example specification of a VerboseQuestion extension:
```JS
{
  "version": "1.0.0",
  "name": "VerboseQuestion",
  "questions": [
    {
      "questionCode": "BusinessFunction-Practice-Stream-Level",
      "text": "The question text",
      "quality": [
        "A list of the quality criteria"
      ]
    }
  ]
}

```

Here is an example of a .samm file with the VerboseQuestion extensions:

```JS
{
  "formatVersion": "1.0.0",
  "assessment": {
    "version": "2.0.8",
    "organisation": "Acme Corporation",
    "scope": "Team Coyote",
    "date": "2024-07-07",
    "answers": [ {...} ... ] // 90 SAMM Answers in total
  },
  "extensions": [
    {
      "name": "verboseQuestion",
      "version": "1.0.0",
      "questions": [
        {
          "questionCode": "G-SM-A-1",
          "text": "Do you understand the enterprise-wide risk appetite...",
          "quality": [
            "You capture the risk appetite...",
            ...
          ]
        },
        ...
      ]
    }
  ]
}
```

In a similar way one could define a VerboseAnswer extension.

Then a parser can easily combine the SAMM Answers in the core data with the extension data to create the verbose representation mentioned earlier. While a more basic parsers can just ignore it and extract the core data.

Here is an example of a .samm file with a hypothetical Benchmark extension:

```JS
{
  "formatVersion": "1.0.0",
  "assessment": {
    "version": "2.0.8",
    "organisation": "Acme Corporation",
    "scope": "Team Coyote",
    "date": "2024-07-17",
    "answers": [ {...} ... ] // 90 SAMM Answers in total
  },
  "extensions": [
    {
      "name": "Benchmark",
      "version": "1.0.0",
      "assessmentType": "Self",
      "assessmentScope": "Team",
      "geographicRegion": "Global",
      ...
    }
  ]
}
```

