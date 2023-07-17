# FastPDF Python Package

Welcome to **fastpdf** Python Package, the versatile and quick solution for your PDF rendering needs. This package is a SDK for fastpdfservice.com REST API.

## Overview

`fastpdf` is a Python package designed to simplify the PDF generation process. It streamlines the procedure of rendering HTML/CSS templates into a PDF document, offering granular control over the rendering options, and provides the ability to add images and barcodes into your PDFs.

`fastpdf` leverages the PDF manipulation API available at [fastpdfservice.com](https://www.fastpdfservice.com), ensuring high performance, scalability, and reliability. By using fastpdf, you are able to generate complex, high-quality PDFs efficiently. 

## Key Features

1. **HTML/CSS to PDF**: Render your HTML/CSS templates into a PDF with ease and precision.
2. **Header/Footer Management**: Easily manage the header and footer of your documents with flexible rendering options.
3. **Barcode & Image Integration**: Seamlessly integrate images and barcodes into your PDFs.
4. **Render Options**: Advanced render options allow granular control over your output. Control aspects like margins, orientation, scales, etc.
5. **Image Mode Selection**: fastpdf supports various image modes to tailor the image rendering to your needs.

## Getting Started

Before you start with the fastpdf Python Package, make sure you have Python 3.6+ installed on your machine.

To use `fastpdf`, you'll first need to register an account to [fastpdfservice.com](https://www.fastpdfservice.com), and get your API key. 

Install fastpdf using pip:
```shell
pip install fastpdf
```

Import the package in your Python script:

```python
from fastpdf import PDFClient
```

To start with, make sure you have your FastPDF service API token handy. 
This is required for all API calls to the FastPDF service. You can find your token under API settings in 
your [FastPDF profile](https://fastpdfservice.com/profile/api).

<CodeGroup title="Register your token">

```python {{ title: 'Python' }}
client = PDFClient("API_KEY")
```

</CodeGroup>


Please refer to the [documentation](https://docs.fastpdfservice.com) for more details on the available features.


## Creating a Basic Template

Creating a basic template is simple. Let's start by creating an HTML document with a placeholder for a `name` variable.
Then, you'll need to create a [Template](templates#template) object and set `name` and `format`.


<CodeGroup title="Creating a basic template">

```python {{ title: 'Python' }}
from fastpdf import Template

template_str = "<html><body>Hello world, my name is {{name}}!</body></html>".encode()
template_data = Template(name="basic-document", format="html")
template = client.add_template(template_str, template_data)
```

</CodeGroup>

<Note>
  Make sure you do not forget to `encode()` your string. Otherwise, the string will be interpreted as a filename.
</Note>

In this template, `{{name}}` is a placeholder that will be replaced by a real value when generating a PDF. 

## Your First Rendering

Now, let's bring our document to life. We've got our template id handy, and our data ready to be cast into the chosen template. 
But don't worry if you've misplaced the template id - a quick detour to your [template dashboard](https://fastpdfservice.com/services/templates) will point you right to it.
In our data object, we set a value for the `name` variable that we defined in our template.

<CodeGroup title="Rendering our basic template">

```python {{ title: 'Python' }}
template_id = template["id"]
document_data = { 'name': 'John' }
document = client.render_template(template_id, document_data)
```

</CodeGroup>

Finally, we save it to the disk using the `save()` convenience function.

<CodeGroup title="Saving our document to the disk">

```python {{ title: 'Python' }}
client.save(document, "path/to/basic-document.pdf")
```

</CodeGroup>

And that's it! You've just brought your first PDF to life.


## For Loops

FastPDF allows for more complex templates. For instance, you can use control structures like for loops and if statements in your templates.
Here is how to iterate over a list of items, using a for loop.

<CodeGroup title="For Loop">

```python {{ title: 'Python' }}
template_content = """
<html>
<body>
    <ul>
    {% for item in items %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
</body>
</html>
""".encode()
template_data = Template(name="for-loop-document", format="html")
template = client.add_template(template_content, template_data)
```

</CodeGroup>

In this template, `{% for item in items %}` starts the loop, and `{% endfor %}` ends it.

Next, we define our rendering_data by associating a list of item value to the `items` key, and render our document.

<CodeGroup title="Rendering our For Loop template">

```python {{ title: 'Python' }}
template_id = template["id"]
document_data = { 'items': ["First item", "Second item", "Last item"] }
document = client.render_template(template_id, document_data)
client.save(document, "path/to/loop-document.pdf")
```

</CodeGroup>


## If Statements

If statements can be used to conditionally include parts of the template, based on the rendering data. Here's an example:

<CodeGroup title="If statement">

```python {{ title: 'Python' }}
template_content = """
<html>
<body>
    {% if user_premium %}
        <p>Welcome, premium user!</p>
    {% else %}
        <p>Welcome, regular user!</p>
    {% endif %}
</body>
</html>
""".encode()
template_data = Template(name="if-document", format="html")
template = client.add_template(template_content, template_data)
```

</CodeGroup>

In this example, `{% if user_premium %}` starts the if statement, and `{% endif %}` ends it. 
If `user_premium` is true, the "Welcome, premium user!" message will be included in the generated PDF. 
Otherwise, the "Welcome, regular user!" message will be included.

## Adding an image

FastPDF allows you to add images to your templates, which can then be rendered into your final PDF documents. Here's how you can accomplish this.

To begin, we create our template with an `<img>` tag, using our image uri in the `src` property as followed:

<CodeGroup title="Creating a template with an image">

```python {{ title: 'Python' }}
template_content = """
<html>
<body>
    <img src="{{my_favourite_logo}}">
    <p>Welcome, {{name}}!</p>
</body>
</html>
""".encode()
template_data = Template(name="image-document", format="html")
template = client.add_template(template_content, template_data)
```

</CodeGroup>

Then, we add an image to our template, and set the same image uri. 
This step can be done from your [template dashboard](https://fastpdfservice.com/services/templates).

<CodeGroup title="Adding an image">

```python {{ title: 'Python' }}
from fastpdf import ImageFile

image_path="my-logo.png"
image_data = ImageFile(format="png", uri="my_favourite_logo")
template_id = template["id"]
res = client.add_image(template_id, image_path, image_data)
```

</CodeGroup>

Finally, we render our document. As usual, we define our rendering data object with our variable. 

<CodeGroup title="Rendering our image template">

```python {{ title: 'Python' }}
template_id = template["id"]
document_data = { 'name': "Jane" }
document = client.render_template(template_id, document_data)
client.save(document, "path/to/image-document.pdf")
```

</CodeGroup>

## Generating Multiple PDFs

The FastPDF API also provides the ability to generate multiple PDFs in a single API call. This feature, referred to as batch processing, can be useful in situations where you need to generate many PDFs with different data.

The way batch processing works in FastPDF is that you provide an array of data objects, each of which corresponds to one PDF. Let's see how to do this in practice.

First, let's prepare our template and data. Suppose we want to generate PDFs for each user in our system.

<CodeGroup title="Basic template">

```python {{ title: 'Python' }}
template_content = """
<html>
<body>
    <h1>Welcome, {{name}}!</h1>
    <p>Email: {{email}}</p>
</body>
</html>
""".encode()
template_data = Template(name="image-document", format="html")
template= client.add_template(template_content, template_data)
```

</CodeGroup>

Here, the template contains placeholders for user data (name and email). Next, we will prepare our data array.
Each element of the array corresponds to one document. Here we will render 3 documents.

<CodeGroup title="List of data">

```python {{ title: 'Python' }}
users = [
    {"name": "John Doe", "email": "john@example.com"},
    {"name": "Jane Doe", "email": "jane@example.com"},
    {"name": "Richard Roe", "email": "richie@example.com"},
]
```

</CodeGroup>

Finally, we can call the `render_template_many()` method with our template id and batch data.

<CodeGroup title="List of data">

```python {{ title: 'Python' }}
template_id = template["id"]
zip_result = client.render_template_many(template_id, users)
```

</CodeGroup>

The `render_template_many()` method will return a zip file that contains the 3 PDFs. 
You now have the choice between extracting the zip_result into a python list, or in a directory on your disk, both using the `extract()` method.

<CodeGroup title="Extracting result PDFs">

```python {{ title: 'Python' }}
# Save pdfs to the disk
client.extract(zip_result, "path/to/directory/")

# Or get a list of PDF
pdfs = client.extract(zip_result)
```
</CodeGroup>



## Support

For any questions or support, reach out to us at `support@fastpdfservice.com`.

## License

fastpdf Python Package is licensed under the MIT license. See LICENSE for more details.


