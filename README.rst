IPython File Upload
===================

.. image:: https://img.shields.io/pypi/v/fileupload.svg
    :target: https://pypi.python.org/pypi/fileupload/
    :alt: Latest Version

.. image:: https://img.shields.io/pypi/dm/fileupload.svg
    :target: https://pypi.python.org/pypi/fileupload/
    :alt: Downloads

.. image:: https://beta.mybinder.org/badge.svg
    :target: https://beta.mybinder.org/v2/gh/draperjames/fileupload2binder/master?filepath=index.ipynb
    :alt: Binder

An IPython notebook widget to upload files, using FileReader_.

Installation
------------

Install using pip::

    pip install fileupload

Install JavaScript::

    jupyter nbextension install [--user] --py fileupload

Enable the extension::

    jupyter nbextension enable [--user] --py fileupload

Usage
-----

.. code-block:: python

    import io
    from IPython.display import display
    import fileupload

    def _upload():

        _upload_widget = fileupload.FileUploadWidget()

        def _cb(change):
            decoded = io.StringIO(change['owner'].data.decode('utf-8'))
            filename = change['owner'].filename
            print('Uploaded `{}` ({:.2f} kB)'.format(
                filename, len(decoded.read()) / 2 **10))

        _upload_widget.observe(_cb, names='data')
        display(_upload_widget)

    _upload()


Base64 data is synced to the ``data_base64``  member, decoded data can be
obtained from ``data``.
The name of the uploaded file is stored in ``filename``.

This example shows how you can create a temporary file on the local file system which can be directly read

.. code-block:: python

    import fileupload, os, tempfile
    def upload_as_file_widget(callback=None):
        """Create an upload files button that prints the file name and file size.
        """

        _upload_widget = fileupload.FileUploadWidget()
        def _virtual_file(change):
            file_ext = os.path.splitext(change['owner'].filename)[-1]
            print('Uploaded `{}`'.format(change['owner'].filename))
            if callback is not None:
                with tempfile.NamedTemporaryFile(suffix=file_ext) as f:
                    f.write(change['owner'].data)
                    callback(f.name)

        _upload_widget.observe(_virtual_file, names='data')

        return _upload_widget
        
    def file_info(in_path):
        print('uploaded to ',in_path, 'infos', os.stat(in_path))
        
    upload_as_file_widget(file_info)

Changelog
---------

Refer to Changelog_.

.. _FileReader: https://developer.mozilla.org/en-US/docs/Web/API/FileReader
.. _Changelog: ./ChangeLog
