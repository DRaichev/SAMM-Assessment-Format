# What?

A proposal for a new file format for SAMM assessments - .samm  
It will be a JSON syntax file, with a strictly defined structure.

# Why?

The current de facto standard format is an Excel spreadsheet, which is very human-readable.   
However, it is not so easy to parse, takes much more space than necessary, and is not easily extensible. By that I mean there are no guidelines for how to extend, so extensions are made ad-hoc. A recent example is the addition of the "Benchmark" sheet. No extension conventions and lack of certainty in the spreadsheet structure means parsers need to be updated often.  
It is also cumbersome to do any kind of data analysis, when the data is hundreds of spreadsheets.   
Last but not least, you need Excel. Yes, the .xlsx format is technically open. Yes, LibreOffice exists. No, the SAMM Toolbox doesn't work in LibreOffice.

# How?
## Formal Specification for the .samm format
The structure of the .samm file format will be specified using [JSON Schema](https://json-schema.org/).  
The reasons for this choice are as follows:
- it is human-readable
- it serves as both a specification and a validation tool
- it is widely used and supported by many tools

The thought process and rationale behind the design of the format is described [here](rationale.md).

A formal specification is available [here](samm.schema.json).

An example of a valid .samm file is available [here](example.samm).

### *Note*:
Not all valid .samm files are actually valid SAMM assessments.  
The schema will validate the file structure, but not the contents.  
For example, one could include a question with a code that does not exist in SAMM.  
Handling of such files is the responsibility of parsers.


# Extensions 

A list of extensions:

*Note – if you want an extension to be added to this list please submit a PR*

- [Framework Extension](https://github.com/DRaichev/SAMM-Assessment-Format-Framework-Extension) - fully supported in [Sammy](https://sammy.codific.com/)
- [Stream Remarks Extension](https://github.com/DRaichev/SAMM-Assessment-Format-Remarks-Extension) - fully supported in [Sammy](https://sammy.codific.com/)
