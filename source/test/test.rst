Test Page
=========
This page can be used for testing.

Here's a nice image:

.. image:: brain.png
  :height: 700px
  :width: 900 px
  :scale: 50 %
  :alt: A music brain
  :align: center

`Read the docs`_, `Sphinx`_ and `reStructuredText`_ are good references for a start.

.. _Read the docs: http://readthedocs.org/
.. _Sphinx: http://sphinx.pocoo.org/
.. _reStructuredText: http://sphinx.pocoo.org/rest.html

Also check out this link: http://www.wikipedia.org

Here's some math:

.. math::

  y = x^2 + 5x + \log{x} \cdot \dfrac{5+3}{x-2}

To build this documentation do:

.. code-block:: bash

  make html


And also, let's look at some C code:

.. code-block:: C

  int a = 5;
  for (int i=1,i<=10,i++) {
    a += i;
  }

and what about python code:

.. code-block:: python3

  def aFunc():
    pass

  if __name__ == "__main__":
    aFunc()
