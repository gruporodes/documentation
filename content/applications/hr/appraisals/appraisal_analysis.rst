==================
Appraisal analysis
==================

To access the *Appraisal Analysis* report, navigate to :menuselection:`Appraisals app --> Reporting
--> Appraisal Analysis`.

On the :guilabel:`Appraisal Analysis` page, there is a report of all the appraisals in the database,
highlighted in different colors to represent their status.

Appraisals in yellow are *Done*, appraisals in orange are in progress (the *Appraisal Sent*, but not
completed), appraisals in red have been *Cancelled*, and appraisals in gray are scheduled *To Start*
(according to the :ref:`appraisals/appraisal-plan`), but have not been confirmed yet.

The report displays the current year, in a default Gantt view, and is grouped by department.

To change the period of time that is presented by default, adjust the date settings in the top-left
of the report. The options to display are :guilabel:`Day`, :guilabel:`Week`, :guilabel:`Month`, and
:guilabel:`Year`. Use the arrows to move forward or backward in time.

At any point, click the :guilabel:`Today` button to have the Gantt view include today's date in the
view.

The report can have other :ref:`filters <search/filters>` and :ref:`groupings <search/group>` set in
the :guilabel:`Search...` bar at the top.

.. image:: reporting/analysis.png
   :align: center
   :alt: A report showing all the appraisals for the Appraisal Analysis report.

.. example::
   Appraisals that have been cancelled appear in red on the :guilabel:`Appraisal Analysis` report,
   but there is no preconfigured filter to show only cancelled appraisals.

   To view only cancelled appraisals, click the :icon:`fa-caret-down` :guilabel:`(caret down)` icon
   in the :guilabel:`Search...` bar.

   Next, click :guilabel:`Add Custom Filter` in the :guilabel:`Filters` section, and a
   :guilabel:`Add Custom Filter` pop up window loads.

   Using the drop-down menu, select :guilabel:`Status` for the first drop-down, then select
   :guilabel:`Cancelled` for the third drop-down field. Click the :guilabel:`Add` button, and only
   appraisals that have been cancelled appear.

   .. image:: reporting/custom-filter.png
      :align: center
      :alt: The Custom Filter pop-up with the parameters set to only show cancelled appraisals.

.. seealso::
   - :doc:`Odoo essentials reporting <../../essentials/reporting>`
   - :doc:`../../essentials/search`
