.. _routines.ma:

Masked array operations
=======================

.. currentmodule:: numpy


Constants
---------

.. autosummary::
   :toctree: generated/

   ma.MaskType


Creation
--------

From existing data
~~~~~~~~~~~~~~~~~~

.. autosummary::
   :toctree: generated/

   ma.masked_array
   ma.array
   ma.copy
   ma.frombuffer
   ma.fromfunction

   ma.MaskedArray.copy
   ma.diagflat


Ones and zeros
~~~~~~~~~~~~~~

.. autosummary::
   :toctree: generated/

   ma.empty
   ma.empty_like
   ma.masked_all
   ma.masked_all_like
   ma.ones
   ma.ones_like
   ma.zeros
   ma.zeros_like


_____

Inspecting the array
--------------------

.. autosummary::
   :toctree: generated/

   ma.all
   ma.any
   ma.count
   ma.count_masked
   ma.getmask
   ma.getmaskarray
   ma.getdata
   ma.nonzero
   ma.shape
   ma.size
   ma.is_masked
   ma.is_mask
   ma.isMaskedArray
   ma.isMA
   ma.isarray
   ma.isin
   ma.in1d
   ma.unique


   ma.MaskedArray.all
   ma.MaskedArray.any
   ma.MaskedArray.count
   ma.MaskedArray.nonzero
   ma.shape
   ma.size


.. autosummary::

    ma.MaskedArray.data
    ma.MaskedArray.mask
    ma.MaskedArray.recordmask

_____

Manipulating a MaskedArray
--------------------------

Changing the shape
~~~~~~~~~~~~~~~~~~

.. autosummary::
   :toctree: generated/

   ma.ravel
   ma.reshape
   ma.resize

   ma.MaskedArray.flatten
   ma.MaskedArray.ravel
   ma.MaskedArray.reshape
   ma.MaskedArray.resize


Modifying axes
~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.swapaxes
   ma.transpose

   ma.MaskedArray.swapaxes
   ma.MaskedArray.transpose


Changing the number of dimensions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.atleast_1d
   ma.atleast_2d
   ma.atleast_3d
   ma.expand_dims
   ma.squeeze

   ma.MaskedArray.squeeze

   ma.stack
   ma.column_stack
   ma.concatenate
   ma.dstack
   ma.hstack
   ma.hsplit
   ma.mr_
   ma.vstack


Joining arrays
~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.concatenate
   ma.stack
   ma.vstack
   ma.hstack
   ma.dstack
   ma.column_stack
   ma.append


_____

Operations on masks
-------------------

Creating a mask
~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.make_mask
   ma.make_mask_none
   ma.mask_or
   ma.make_mask_descr


Accessing a mask
~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.getmask
   ma.getmaskarray
   ma.masked_array.mask


Finding masked data
~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.ndenumerate
   ma.flatnotmasked_contiguous
   ma.flatnotmasked_edges
   ma.notmasked_contiguous
   ma.notmasked_edges
   ma.clump_masked
   ma.clump_unmasked


Modifying a mask
~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.mask_cols
   ma.mask_or
   ma.mask_rowcols
   ma.mask_rows
   ma.harden_mask
   ma.soften_mask

   ma.MaskedArray.harden_mask
   ma.MaskedArray.soften_mask
   ma.MaskedArray.shrink_mask
   ma.MaskedArray.unshare_mask


_____

Conversion operations
----------------------

> to a masked array
~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.asarray
   ma.asanyarray
   ma.fix_invalid
   ma.masked_equal
   ma.masked_greater
   ma.masked_greater_equal
   ma.masked_inside
   ma.masked_invalid
   ma.masked_less
   ma.masked_less_equal
   ma.masked_not_equal
   ma.masked_object
   ma.masked_outside
   ma.masked_values
   ma.masked_where


> to a ndarray
~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.compress_cols
   ma.compress_rowcols
   ma.compress_rows
   ma.compressed
   ma.filled

   ma.MaskedArray.compressed
   ma.MaskedArray.filled


> to another object
~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.MaskedArray.tofile
   ma.MaskedArray.tolist
   ma.MaskedArray.torecords
   ma.MaskedArray.tobytes


Filling a masked array
~~~~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.common_fill_value
   ma.default_fill_value
   ma.maximum_fill_value
   ma.minimum_fill_value
   ma.set_fill_value

   ma.MaskedArray.get_fill_value
   ma.MaskedArray.set_fill_value

.. autosummary::

    ma.MaskedArray.fill_value

_____

Masked arrays arithmetic
------------------------

Arithmetic
~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.anom
   ma.anomalies
   ma.average
   ma.conjugate
   ma.corrcoef
   ma.cov
   ma.cumsum
   ma.cumprod
   ma.mean
   ma.median
   ma.power
   ma.prod
   ma.std
   ma.sum
   ma.var

   ma.MaskedArray.anom
   ma.MaskedArray.cumprod
   ma.MaskedArray.cumsum
   ma.MaskedArray.mean
   ma.MaskedArray.prod
   ma.MaskedArray.std
   ma.MaskedArray.sum
   ma.MaskedArray.var


Minimum/maximum
~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.argmax
   ma.argmin
   ma.max
   ma.min
   ma.ptp
   ma.diff

   ma.MaskedArray.argmax
   ma.MaskedArray.argmin
   ma.MaskedArray.max
   ma.MaskedArray.min
   ma.MaskedArray.ptp


Sorting
~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.argsort
   ma.sort
   ma.MaskedArray.argsort
   ma.MaskedArray.sort


Algebra
~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.diag
   ma.dot
   ma.identity
   ma.inner
   ma.innerproduct
   ma.outer
   ma.outerproduct
   ma.trace
   ma.transpose

   ma.MaskedArray.trace
   ma.MaskedArray.transpose


Polynomial fit
~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.vander
   ma.polyfit


Clipping and rounding
~~~~~~~~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.around
   ma.clip
   ma.round

   ma.MaskedArray.clip
   ma.MaskedArray.round

Set operations
~~~~~~~~~~~~~~
.. autosummary::
   :toctree: generated/


   ma.intersect1d
   ma.setdiff1d
   ma.setxor1d
   ma.union1d


Miscellanea
~~~~~~~~~~~
.. autosummary::
   :toctree: generated/

   ma.allequal
   ma.allclose
   ma.amax
   ma.amin
   ma.apply_along_axis
   ma.apply_over_axes
   ma.arange
   ma.choose
   ma.compress_nd
   ma.convolve
   ma.correlate
   ma.ediff1d
   ma.flatten_mask
   ma.flatten_structured_array
   ma.fromflex
   ma.indices
   ma.left_shift
   ma.ndim
   ma.put
   ma.putmask
   ma.right_shift
   ma.round_
   ma.take
   ma.where
