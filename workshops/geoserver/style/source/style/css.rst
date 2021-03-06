.. _style.css:

Styling with CSS
================

In the last section, we saw how to define a style directly with SLD, manage styles used when publishing a layer, and how styles (and their support files) are stored in the GeoServer data directory.

We will now explore GeoServer styling in greater detail using a tool to generate our SLD files. The **Cascading Style Sheet (CSS)** GeoServer extension is used to generate SLD files using a syntax more familiar to web developers. 

Using the CSS extension to define styles results in shorter examples that are easier to understand. At any point we will be able to review the generated SLD file.

Reference:

* :manual:`CSS Styling <extensions/css/index.html>` (User Manual)

Comparing SLD and CSS
---------------------
   
The CSS extension is built with the same GeoServer rendering engine in mind, providing access to all the functionality of SLD (along with vendor options for fine control of labeling). The two approaches use slightly different terminology: SLD uses terms familiar to mapping professionals, CSS uses ideas familiar to web developers. For example:


SLD
^^^

**SLD** makes use of a series of **Rules** to select content for display. Content is selected using filters that support attribute, spatial and temporal queries.

Once selected, **content is transformed into a shape and drawn using symbolizers**. Symbolizers are configured using CSS Properties to document settings such as "fill" and "opacity".

Content can be drawn by more than one rule, allowing for a range of effects.

Refer to previous sections for examples of SLD.

CSS
^^^

**CSS** also makes use of rules, each rule making use of **selectors** to shortlist content for display. Each selector uses a CQL filter that suports attribute, spatial and temporal queries. Once selected, CSS Properties are used to describe how content is rendered.

Content is not drawn by more than one rule. When content satisfies the conditions of more than one rule the resulting properties are combined using a process called inheritance. This technique of having a generic rule that is refined for specific cases is where the **Cascading** in Cascading Style Sheet comes from.
  
Here is an example of a CSS style:

.. code-block:: css
   
   * {
     mark: url(airport.svg);
     mark-mime: "image/svg";
   }

In this rule the **selector** :kbd:`*` is used to match **all content**. The rule defines **properties** indicating how this content is to be styled. The property :kbd:`mark` is used to indicate we want this content drawn as a **Point**. The value :kbd:`url(airport.svg)` is a URL reference to the image file used to represent each point. The :kbd:`mark-mime` property indicates the expected format of this image file.

Key properties
--------------

As we work through CSS styling examples you will note the use of **key properties**. These properties are required to trigger the creation of an appropriate symbolizer in SLD.

=========== ====================================================
fill        Color (or graphic) for Polygon Fill
stroke      Color (or graphic) for LineString or Polygon border
mark        Well-known Mark or graphic used for Point
label       Text expression labeling
halo-radius Size of halo used to outline label
=========== ====================================================

Using just these key properties and the selector :kbd:`*`, you will be able to visualize vector data.

For example, here is the key property **fill** providing a gray fill for polygon data:

.. code-block:: css
   
   * {
      stroke: gray;
   }

Here is the key property **stroke** providing a blue representation for line or polygon data:

.. code-block:: css
   
   * {
      fill: #2020ED;
   }

Here is the key property **mark** showing the use of the well-known symbol :kbd:`square`:

.. code-block:: css
   
   * {
      mark: symbol(square);
   }
   
Here is the key property **label** generating labels using the :kbd:`CITY_NAME` feature attribute:

.. code-block:: css
   
   * {
      label: [CITY_NAME];
   }
   
Here is the key property **halo-radius** providing an outline around generated label:

.. code-block:: css
   
   * {
      label: [NAME];
      halo-radius: 1;
   }

Reference:

* :manual:`CSS Cookbook <extensions/css/cookbook.html>` (User Manual)
* :manual:`CSS Examples <extensions/css/examples.html>` (User Manual)

Rules and expressions
---------------------

We have already seen the a CSS style composed of a single rule:

.. code-block:: css
   
   * {
     mark: symbol(circle);
   }

We can also make a rule that only applies to a specific FeatureType:

.. code-block:: css
   
   populated_places {
     mark: symbol(triangle);
   }
   
We can make a style consisting of more than one rule, carefully choosing the selector for each rule. In this case we are using a selector to style capital cities with a star, and non-capital with a circle:

.. code-block:: css
   
   [ FEATURECLA = 'Admin-0 capital' ] {
     mark: symbol(star);
     mark-size: 6px;
   }
   
   [ FEATURECLA <> 'Admin-0 capital' ] {
     mark: symbol(circle);
     mark-size: 6px;
   }

The feature attribute test performed above uses **Constraint Query Language (CQL)**. This syntax can be used to define filters to select content, similar to how the SQL WHERE statement is used. It can also be used to define expressions to access attribute values allowing their use when defining style properties.

Rule selectors can also trigger based on the state of the rendering engine. In this example we are only applying labels to once zoomed in:

.. code-block:: css

   [@scale < 20000000] {
      label: [ NAME ];
   }

In the above example the label is defined using the CQL Expression :kbd:`NAME`. This results in a dynamic style that generates each label on a case-by-case basis, filling in the label with the feature attribute :kbd:`NAME`.

Reference:

* :manual:`Filter Syntax <extensions/css/filters.html>` (User Manual)
* :manual:`ECQL Reference <filter/ecql_reference.html>` (User Guide)

Cascading rules
---------------

In our earlier example on feature attribute selection we repeated information. An alternate approach is to make use of CSS **Cascading** and factor out common properties into a general rule:

.. code-block:: css
   
   [ FEATURECLA = 'Admin-0 capital' ] {
     mark: symbol(star);
   }
   
   [ FEATURECLA <> 'Admin-0 capital' ] {
     mark: symbol(circle);
   }
   
   * {
     mark-size: 6px;
   }

Pseudo-selector
^^^^^^^^^^^^^^^

Up to this point we have been styling individual features, documenting how each shape is represented.

When a shape is represented using a symbol, we have a second challenge: documenting the colors and appearance of the symbol. The CSS extension provides a **pseudo-selector** allowing further properties to be applied to a symbol.

Example of using a pseudo-selector:

.. code-block:: css
   
   * {
     mark: symbol(circle);
   }
   
   :mark {
     fill: black;
     stroke: white;
   }

In this example the :kbd:`:mark` pseudo-selector is used select the circle mark, and provides a fill and stroke for use when rendering.

=============== ====================================
Pseudo-selector Use of symbol
=============== ====================================
:mark           point markers
:stroke         stroke patterns
:fill           fill patterns
:shield         label shield
:symbol         any use
=============== ====================================

The above pseudo-selectors apply to all symbols, but to be specific the syntax :kbd:`nth-symbol(1)` can be used:

.. code-block:: css
   
   * {
     mark: symbol(circle);
   }
   
   :nth-mark(1) {
     fill: black;
     stroke: white;
   }

Reference:

* :manual:`Styled Marks <extensions/css/styled-marks.html>` (User Guide)

Installation
------------

The **CSS extension** is distributed as a supported GeoServer extension. GeoServer extensions are unpacked into the ``webapps`` folder of the application server.

This extension should already be installed. Installation instructions are provided here if you missed installing this during initial training setup.

.. only:: windows
   
   Windows
   ^^^^^^^
   
   The **CSS Extension** is included in the Windows installer.

   If you did not select this option initially the installer can be run again to update your GeoServer application.

   .. admonition:: Exercise
   
      #. Stop the GeoServer service. From the :guilabel:`Start` menu select :menuselection:`OpenGeo --> GeoServer --> Stop`. 
      
         .. figure:: /img/install_stop.png

            Stopping GeoServer on Windows
      
      #. Run the installer, and when asked to **Choose Components** select :kbd:`CSS Styling`.
      
         .. figure:: img/install_03_windows.png

            Selecting the CSS Styling component
      
      #. Restart the GeoServer service.
      
         From the :guilabel:`Start` menu select :menuselection:`OpenGeo --> GeoServer --> Start`. 
      
          .. figure:: /img/install_start.png

             Starting GeoServer on Windows
   
      #. Login to the Web Administration application and confirm that **CSS Styles** is listed in the navigation menu.
      
         .. figure:: img/install_05_menu.png

            CSS Styles installed

.. only:: linux
   
   Linux
   ^^^^^
   
   OpenGeo Suite includes the packages for the CSS extension and other supported extensions. 
   
   While we have included quick install instructions here you may wish to check the release documentation more detailed step-by-step instructions.

   .. admonition:: Exercise
   
      #. Stop GeoServer so we can update the application:
   
         .. code-block:: bash
         
            service tomcat6 stop
   
      #. Install the wps archive.
   
         Ubuntu:
      
         .. code-block:: bash
         
            sudo su -
            apt-get install geoserver-css
      
         Red Hat:
      
         .. code-block:: bash
      
            sudo su -
            yum install geoserver-css
      
      #. Restart GeoServer:

         .. code-block:: bash
         
            service tomcat6 start
         
      #. Login to the Web Administration application and confirm that **CSS Styles** is listed in the navigation menu.
      
         .. figure:: img/install_05_menu.png

            CSS Styles installed
      
Application Server
^^^^^^^^^^^^^^^^^^

If you are working with GeoServer deployed into your own application server you can download and install the CSS extension by hand.

.. admonition:: Exercise

   #. Download geoserver-2.4-css-plugin.zip from:
   
      * `Stable Release <http://geoserver.org/display/GEOS/Stable>`_ (GeoServer WebSite)
      * `Nightlight Build <http://ares.boundlessgeo.com/geoserver/2.4.x/ext-latest/>`_ (Build Box)
   
   #. Stop the application server.

      Tomcat 6 example:
   
      .. code-block:: bash
         
         service tomcat6 stop

   #. Navigate into the :file:`geoserver/WEB-INF/lib` folder.
   
      These files make up the running GeoServer application.
   
   #. Unzip the contents of geoserver-2.4-css-plugin.zip into :file:`lib` folder.

   #. Restart the Application Server.
   
      Tomcat 6 example:
   
      .. code-block:: bash
      
         service tomcat6 start
      
   #. Login to the Web Administration application and confirm that **CSS Styles** is listed in the navigation menu.
      
      .. figure:: img/install_05_menu.png

         CSS Styles installed

To confirm everything works, let's reproduce our airports style from the previous section.

.. admonition:: Exercise

   #. Navigate to the **CSS Styles** page. This page works with two selections (style being edited and data used for preview) and provides a number of actions for style management.
      
      .. figure:: img/css_01_actions.png

         CSS Styles page

   #. Change our preview data to `training:airports`. This dataset will be used as a reference as we define our style.
   
      Click :guilabel:`Choose a different layer` and select :kbd:`training:airports` from the list.
   
      .. figure:: img/css_02_choose_data.png

         Choosing the airports layer

   #. Each time we edit a CSS style, the contents of the associated SLD file are replaced. Rather then disrupt any of our existing styles we will create a new style.

      Click :guilabel:`Create a new style` and choose the following:
   
      .. list-table:: 
         :widths: 30 70
         :stub-columns: 1

         * - Workspace for new layer:
           - :kbd:`No workspace`
         * - New style name:
           - :kbd:`airport2`
        
      .. figure:: img/css_03_new_style.png

         New style
      
   #. The initial style sheet is:
   
      .. code-block:: css
      
         * { fill: lightgrey; }
      
      Replace this definition with our airport CSS example:

      .. code-block:: css
   
         * {
           mark: url(airport.svg);
           mark-mime: "image/svg";
         }
   
      .. figure:: img/css_04_edit.png

         Editing the CSS example
   
   #. Click :guilabel:`Submit` to try out the style.

   #. The :guilabel:`Generated SLD` shows the SLD file produced.
   
      .. figure:: img/css_05_generated.png

         Generated SLD
   
   #. Click :guilabel:`Map` to preview using the selected data. You can use the mouse buttons to pan and scroll wheel to change scale.

      .. figure:: img/css_06_preview.png

         Layer preview
   
   #. Click :guilabel:`Data` for a summary of the selected data.
   
      .. figure:: img/css_07_data.png

         Layer attributes
   
   #. The :guilabel:`CSS Reference` tab embeds the :manual:`CSS Styling <extensions/css/index.html>` section of the user manual.

.. admonition:: Explore

   #. Return to the :guilabel:`Data` tab and use the :guilabel:`Compute` link to determine the minimum and maximum for the **scalerank** attribute.
      
      .. only:: instructor
       
         .. admonition:: Instructor Notes             
  
            Should be 2 and 9 respectively.
       
   #. Try experimenting with the size of the icon:

      .. code-block:: css
   
         * {
           mark: url(airport.svg);
           mark-mime: "image/svg";
           mark-size: 22;
         }

.. admonition:: Challenge

   #. Create a CSS style for ``ne:ports`` and compare the result to your earlier work. How does the generated style differ from what we created by hand?
      
      .. only:: instructor
       
         .. admonition:: Instructor Notes      
 
            Generated SLD does not include name or title information; this can be added by students using an annotation. Encourage students to look this up in the reference material provided.
         
            The second difference is with the use of a fallback Mark when defining a PointSymbolizer. The CSS extension does not bother with a fallback as it knows the capabilities of the GeoServer rendering engine (and is not trying to create a reusable style).

   #. How would you change from the SVG to PNG image?
      
      .. only:: instructor
       
         .. admonition:: Instructor Notes      
 
            .. code-block:: css
   
               * {
                 mark: url(airport.png);
                 mark-mime: "image/png";
                 mark-size: 22;
               }
