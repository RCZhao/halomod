-------
halomod
-------

``halomod`` is a python application that provides a flexible and simple interface for
dealing with the Halo Model of Dark Matter Halos.

Disclaimer/Note
---------------
``halomod`` is currently undergoing some pretty heavy development, and so may
change reasonably significantly at short notice. If you are using it, and have any
problems, don't hesitate to contact me (@steven-murray) and I'll walk you through anything
that I can help with. Thanks for your patience. Also, for the most up-to-date version,
grab the ``develop`` branch of this repo.

** Note that for version 1.5.0+, halomod requires hmf v2.1.0+, and the procedure for using ``camb`` is different. Please
see the hmf docs for details.**

To be more clear, there are several features which are still under development and so should
either appear or be more robust soon, including:

* Documentation (sphinx + readthedocs)
* Tests
* A simpler conceptual split between the dark matter halo model, and the HOD.
* Angular correlation functions work, but are a bit hacky at the moment
* Robust support for multi-epoch calculations

Installation
------------
Almost all of the dependencies for ``halomod`` will be installed automatically. However, there
are a few optional dependencies which can make things better if you decide to install them:

* `camb`: for using CAMB to generate transfer functions See
  `documentation <http://hmf.readthedocs.io/en/latest/>` of `hmf` for instructions.
* `Numba`: to accelerate some of the integrations when halo exclusion is involved.

Also, at this point, you'll need a fortran compiler. We plan on removing this as a necessity in
future versions (but you will still need it if you want to use CAMB, of course).

To install, simply use ``pip install git+git://github.com/steven-murray/halomod``.

For the development version (at this stage, it may be actually be better to get this), tack a
``@develop`` on the end of the url.

Features
--------
* All the features of `hmf` (several transfer function models, 15+ HMF fitting functions,
  efficient caching etc.)
* Extended components for halo model:
    * 10 halo bias models, plus scale-dependent bias from Tinker (2005).
    * 3 basic concentration-mass-redshift relations, including the analytic Bullock (2001) model
    * Several plug-and-play halo-exclusion models (including ng-matched from Tinker+2005).
    * 5 built-in HOD parameterisations
    * Many built-in halo profiles, including NFW, generalised NFW, Hernquist etc.
    * Support for WDM models.
* All basic quantities such as 3D correlations and power spectra, and projected 2PCF.
* Several derived quantities (eg. effective bias, satellite fraction etc.)
* All models/components specifically written to be easily extendible
* Simple routine for populating a halo catalogue with galaxies via a HOD.
* Built-in routines for efficient fitting of arbitrary parameters to data. **BETA**
* CLI scripts both for producing any quantity included, or fitting any quantity. **BETA**

Usage
-----
``halomod`` can be used interactively (for instance in ``ipython`` or a ``jupyter`` notebook)
or in a script.
To use interactively, in ``ipython`` do something like the following:

>>> from halomod import HaloModel
>>> hm = HaloModel() ## Construct the object
>>> help(hm)         ## Lists many of the available quantities.
>>> galcorr = hm.corr_gg
>>> bias = hm.bias
>>> ...

All parameters to ``HaloModel`` have defaults so none need to be specified. There are
quite a few that *can* be specified however. Check the docstring to see the
details. Furthermore, as ``halomod`` extends the functionality of ``hmf``, almost all
parameters accepted by ``hmf.MassFunction()`` can be used (check its docstring).

To change the parameters (cosmological or otherwise), one should use the 
``update()`` method, if a ``HaloModel()`` object already exists. For example

>>> hm.update(rmin=0.1,rmax=1.0,rnum=100) #update scale vector
>>> corr_2h = hm.corr_gg_2h #The 2-halo term of the galaxy correlation function

Since ``HaloModel`` is a sub-class of ``MassFunction``, all the quantities associated
with the hmf are also included, so for example

>>> mass_variance = hm.sigma
>>> mass_function = hm.dndm
>>> linear_power = hm.power

We have tried to stick to reasonable naming conventions for the quantities. In particular,
galaxy-galaxy correlations are labelled ``xxx_gg_xxx``, matter correlations
``xxx_mm_xxx``, central-satellite have ``xxx_cs_xxx`` and satellite-satellite ``xxx_ss_xxx``.

In addition, all parameters passed to the constructor are saved within the class with the *same name*
as they are passed with. Any parameter which defines a model choice (eg. a bias model) is named ``<component>_model``,
so for example, the bias model is called ``bias_model``. *Every* model has an associated parameter called
``<component>_params``, which is a dictionary of parameters to that model. The available choices for this
dictionary depend precisely on the model chosen (so that the Sheth-Tormen HMF has a different set of parameters
than does the Tinker+2008 model). Within the constructed object, the actual model is instantiated and saved
as ``<component>``, so that this object can be accessed, and several internal methods can be called. *Some* of these
are exposed directly by the ``HaloModel`` class (eg. one can call ``hm.n_sat`` directly, which itself calls a method
of the ``hm.hod`` component).

Acknowledgments
---------------
Thanks to Florian Beutler, Chris Blake and David Palamara
who have all contributed significantly to the ideas, implementation and testing
of this code.

Some parts of the code have been adapted from, influenced by or tested against:

* chomp (https://github.com/JoeMcEwen/chomp)
* AUM  (https://github.com/surhudm/aum)
* HMcode (https://github.com/alexander-mead/HMcode/)

Along with these, several other private codes have been compared to.


