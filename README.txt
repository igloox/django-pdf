What is this?
-------------

A lightweight django middleware app to convert the HTML output of a view to PDF. 
Originally forked from https://github.com/directeur/django-pdf
This latest version uses PhantomJS to generate PDFs (rather then xhtml2pdf)

Installation
------------

1. Install PhantomJS. See http://phantomjs.org/

2. Install egg (via pip http://www.pip-installer.org/)

       pip install -e git+https://github.com/chrispbailey/django-pdf#egg=django-pdf

3. List this application in the ``INSTALLED_APPS`` portion of your settings
   file.  Your settings file might look something like::
   
       INSTALLED_APPS = (
           # ...
           'django_pdf',
       )

4. Install the pdf middleware. Your settings file might look something
   like::
   
       MIDDLEWARE_CLASSES = (
           # ...
           'django_pdf.middleware.PdfMiddleware',
       )

5. If it's not already added in your setup, add the request context processor.
   Note that context processors are set by default implicitly, so to set them
   explicitly, you need to copy and paste this code into your under
   the value TEMPLATE_CONTEXT_PROCESSORS::
   
        ("django.core.context_processors.auth",
        "django.core.context_processors.debug",
        "django.core.context_processors.i18n",
        "django.core.context_processors.media",
        "django.core.context_processors.request")

6. Add the django_pdf's context processor

    TEMPLATE_CONTEXT_PROCESSORS=(
        "django.core.context_processors.auth",
        "django.core.context_processors.debug",
        "django.core.context_processors.i18n",
        "django.core.context_processors.media",
        "django.core.context_processors.request",
        "django_pdf.context_processors.check_format", #<-- this line
    )

7. In your settings.py file, specify the path to the PhantomJS binary

    # Location of phantomjs executable
    PHANTOMJS_EXECUTABLE = "/path/to/phantomjs"

8. (Optional) specify a custom javascript file (for per-project page
   sizes, margins, headers, footers, etc)

    # Location of phantomjs javascript
    PHANTOMJS_SCRIPT = "/path/to/script.js"


That's it, now all it takes to generate a PDF version of your page is to add:
?format=pdf to your urls

Example:
    http://127.0.0.1/contacts/list displays Contacts' list.
    http://127.0.0.1/contacts/list?format=pdf returns the pdf version of it.


Templates
---------

You may ask: "What if I don't want to include some parts of the HTML page
in the PDF output? (like a menu)" 
You'd be right, and the answer is easy:
Use the variable DJANGO_PDF_OUTPUT in your template which will be set to True if
the PDF is requested and to False otherwise.

Example:
    {% if not DJANGO_PDF_OUTPUT %}
        <ul id="menu">
            <li>menu item</li>
            <li>menu item</li>
            <li>menu item</li>
        </ul>
    {% endif %}

Also, you can use {% if DJANGO_PDF_OUTPUT %} to include some parts only in the
PDF output.


Bonus:
------

If you load the pdf_tags file into your template code (with {% load pdf_tags %}) 
you will have access to a new template tag {% pdf_url %} which will generate a url
to the PDF version of the current page. :)

P.S.

The string "format=pdf" and the variable DJANGO_PDF_OUTPUT are customizable in
your settings.

Look:

REQUEST_FORMAT_NAME = getattr(settings, 'REQUEST_FORMAT_NAME', 'format')
REQUEST_FORMAT_PDF_VALUE = getattr(settings, 'REQUEST_FORMAT_PDF_VALUE', 'pdf')
TEMPLATE_PDF_CHECK = getattr(settings, 'TEMPLATE_PDF_CHECK','DJANGO_PDF_OUTPUT')

That's it!
