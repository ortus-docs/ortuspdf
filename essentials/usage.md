# Usage

The Ortus PDF extension will give you the following available tags:

* `cfpdfform`
* `cfpdfformparam`

### cfpdfform/cfpdfformparam

This tag allows you to manipulate existing PDF forms, usually edited in Adobe Acrobat or LiveCycle Designer. You will be able to do the following:

* Embed a form with pre-filled FORM data and deliver it via `cfcontent or cfpdf or cfdocument` tags
* Render out a pre-filled FORM to disk with overwrite and flattening capabilities.
* Extract values from a PDF form as:
  * CFML Structure
  * XML string
  * XML file
  * FDF file

#### **Basic Usage**

```xml
<cfpdfform
    action = "populate"
    source = "PDF file pathname|byte array"
    destination = "output file pathname"
    overwrite = "yes|no"
    overwriteData = "yes|no"
    xmlData = "XML object|XML string|XML data filename"
    structData = "CFML Struct">
    
    <cfpdfformparam name="" value="" index="" />

</cfpdfform>

<cfpdfform
    action = "read"
    source = "pathname|byte array"
    result = "structure containing form field values"
    overwrite = "yes|no"
    xmlData = "variable containing xml values or an XML file path"
    fdfData = "The absolute file path to export the data as FDF"> 
</cfpdfform>
```

The `cfpdfform` tag can have nested `cfpdfformparam` tags in order to populate forms inside PDF documents.

### **`cfpdfform` attributes**

| **Parameter**     | **Required** | **Action**       | **Default** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----------------- | ------------ | ---------------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **action**        | Yes          | ---              | `read`      | Action to perform: `populate or read`                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| **destination**   | Yes          | `populate`       | ---         | Pathname for the output file. You can specify an absolute pathname or a pathname relative to the context root. The file extension must be PDF. Do not specify the destination when you embed a form in a PDF document.                                                                                                                                                                                                                                                        |
| **overwrite**     | No           | `populate, read` | No          | Specifies whether to overwrite the destination file (if action="populate") or the data file (if action="read"):                                                                                                                                                                                                                                                                                                                                                               |
| **overwriteData** | No           | `populate`       | No          | If enabled it will overwrite form field values, else it will only override them if the value is empty.                                                                                                                                                                                                                                                                                                                                                                        |
| **result**        | No           | `read`           | `pdfdata`   | ColdFusion structure that contains the form field values. If not used, the tag will create a variable called `pdfdata` for you in the `caller` scope.                                                                                                                                                                                                                                                                                                                         |
| **source**        | Yes          | `populate, read` | ---         | Pathname of the source PDF (absolute on-disk or in-memory path, or path relative to the context root) or byte array representing a PDF.                                                                                                                                                                                                                                                                                                                                       |
| **flatten**       | No           | `populate`       | true        | Will flatten the populated PDF so the form is sealed in the container PDF.                                                                                                                                                                                                                                                                                                                                                                                                    |
| **structData**    | No           | `populate`       | ---         | Instead of using nested `cfpdfformparam` tags, you can pass a struct of name-value pairs to populate a PDF form with.                                                                                                                                                                                                                                                                                                                                                         |
| **jsonData**      | No           | `populate,read`  | ---         | <p><strong>Populate</strong>: Instead of using nested <code>cfpdfformparam</code> tags, you can pass a JSON object of name-value pairs to populate a PDF form with.<br><strong>Read</strong> : If a string, this will be the name of the variable in the caller's <code>variables</code> scope that will contain the form data as a JSON string.</p>                                                                                                                          |
| **xmlData**       | No           | `read, populate` | ---         | <p><strong>Read</strong> : If a string, this will be the name of the variable in the caller's <code>variables</code> scope that will contain the Form data as an XML packet value. If this is a file path ending in <code>.xml</code> then the tag will export the XMl of the Form data to that file.<br><strong>Populate</strong> : The XML object, XML string or XML data filename to use when populating the PDF form instead of sub <code>cfpdfformparam</code> tags.</p> |
|                   |              |                  |             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |

### **`cfpdfformparam` attributes**

| **Parameter** | **Required** | **Default** | **Description**                  |
| ------------- | ------------ | ----------- | -------------------------------- |
| **name**      | Yes          | ---         | The name of the PDF form entry   |
| **value**     | Yes          | ---         | The value of the PDF form entry  |
| **index**     | ---          | ---         | ''Not implemented by Ortus PDF'' |

#### **Examples**

**Populating a PDF Form with sub params**

```xml
<cfpdfform 
    action="populate" 
    source="#datapath#/report.pdf" 
    destination="#workpath#/report-out.pdf" 
    overwrite="true"
    overwriteData="true"
    result="myresult"
    flatten="true">
    <cfpdfformparam name="full_name" value="Ortus Rocks" />
    <cfpdfformparam name="exam_date" value="#dateFormat( now(), "mmmm dd, yyyy")# #timeFormat( now(), "medium")#" />
    <cfpdfformparam name="strategy_overview" value="Drink a lot of water!" />
    <cfpdfformparam name="strategy_tips" value="Eat a lot of cheese!" />
</cfpdfform>

<!-- Deliver the file -->
<cfcontent file="#workpath#/report-out.pdf" type="application/pdf" reset="true" />
```

**Populating a PDF Form with a struct**

```xml
<cfset data = {
    "full_name" 		= "Ortus Rocks",
    "exam_date" 		= "#dateFormat( now(), "mmmm dd, yyyy")# #timeFormat( now(), "medium")#",
    "strategy_overview" = "Drink a lot of water!",
    "strategy_tips" 	= "Eat a lot of cheese!"
}>
<cfpdfform 
    action="populate" 
    source="#datapath#/report.pdf" 
    overwrite="true"
    overwriteData="true"
    structData=data
    flatten="true">
</cfpdfform>
```

**Populating a PDF Form with a JSON object**

```xml
<cfset jsonData = '{
    "full_name" 		: "Ortus Rocks",
    "exam_date" 		: "#dateFormat( now(), "mmmm dd, yyyy")# #timeFormat( now(), "medium")#",
    "strategy_overview" : "Drink a lot of water!",
    "strategy_tips" 	: "Eat a lot of cheese!"
}'>
<cfpdfform 
    action="populate" 
    source="#datapath#/report.pdf" 
    overwrite="true"
    overwriteData="true"
    jsonData="#jsonData#"
    flatten="true">
</cfpdfform>
```

**Populating a PDF Form from and XML file and stream to browser**

```xml
<cfpdfform 
    action="populate" 
    source="#datapath#/report.pdf" 
    overwrite="true"
    overwriteData="true"
    xmlData="c:\temp\data.xml"
    flatten="true">
</cfpdfform>
```

**Reading a PDF Form**

```xml
<cfpdfform 
    action="read"
    source="#dataPath#/report.pdf"
    result="formData">

<cfdump var="#formData#">
```

**Reading a PDF Form as JSON Data**

```xml
<cfpdfform 
    action="read"
    source="#dataPath#/report.pdf"
    jsonData="formData">

<cfdump var="#isJSON( formData )#">
<cfdump var="#formData#">
```

**Reading a PDF Form as XML Data**

```xml
<cfpdfform 
    action="read"
    source="#dataPath#/report.pdf"
    xmlData="formData">

<cfdump var="#isXML( formData )#">
<cfdump var="#formData#">
```

**Reading a PDF Form as XML Data into an XML output file**

```xml
<cfpdfform 
    action="read"
    source="#dataPath#/report.pdf"
    xmlData="c:\temp\export.xml">
```

**Reading a PDF Form in FDF format and outputting to a file**

```xml
<cfpdfform 
    action="read"
    source="#dataPath#/report.pdf"
    fdfData="c:\temp\export.fdf">
```

**XML Data**

If you will be populating forms or extracting forms, the format in XML will be the following:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<fields
><exam_date
>October 31, 2013 10:00am</exam_date
><full_name
>Populated by XML</full_name
><keystrengths
>After training, the dimensions of Radical Innovation upon which 
you performed highest were RECEPTIVITY and SELF-INQUIRY.</keystrengths
></fields>
```

\
