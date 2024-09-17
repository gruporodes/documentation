===========
Offboarding
===========

When an employee leaves the company, it is important to ensure their employee record is updated to
reflect their departure, log the reason why, close any open activities associated with the
employee, and provide them with any important documents.

Archive an employee
===================

In Odoo, when an employee leaves the company they must be *archived*. To archive an employee, first
navigate to the main **Employees** app. From here, locate the employee who is leaving the company,
and click on their employee card.

The employee form loads, displaying all their information. Click the :icon:`fa-gear`
:guilabel:`(gear)` icon in the top-left corner, and a drop-down menu appears. Click
:icon:`oi-archive` :guilabel:`Archive`, and an :guilabel:`Employee Termination` pop-up window
appears.

Fill out the following fields on the form:

- :guilabel:`Departure Reason`: Select a reason the employee is leaving from the drop-down menu. The
  default options are:

  - :guilabel:`Fired`: Select this option when an employee is being let go, and the company has
    given notice.
  - :guilabel:`Resigned`: Select this option when the employee no longer wishes to be employed, and
    the employee has given notice.
  - :guilabel:`Retired`: Select this option when the employee is retiring.
  - :guilabel:`Became Freelance`: Select this option when the employee is no longer working for the
    company, but is becoming a freelance worker instead.

- :guilabel:`Contract End Date`: Using the calendar selector, select the last day the employee is
  working for the company.
- :guilabel:`Detailed Reason`: Enter a short description for the employee's departure.
- :guilabel:`Close Activities`: Tick the checkbox next to each type of activity to close or delete
  any open activities associated with it. It is recommended to tick **all** boxes that are
  applicable. The available options are:

  - :guilabel:`Appraisals`: Cancels all appraisals scheduled after the contract end date.
  - :guilabel:`Contract`: Applies an end date for the current contract.
  - :guilabel:`Company Car`: Removes the employee as the driver for their current company car, and
    :ref:`assigns the next driver <fleet/new_vehicle/new-driver>`, if applicable.
  - :guilabel:`Time Off`: Cancels any time off requests after the contract end date.
  - :guilabel:`Allocations`: Removes the employee from any accrual plans they are on.

- :guilabel:`HR Info`: Tick the box next to :guilabel:`Send Access Link` to send a download link to
  the employee's personal email address, containing all their personal HR files they own.
- :guilabel:`Private Email`: This field appears if the :guilabel:`HR Info` checkbox is ticked. Enter
  the private email address for the employee.

When the form is complete, click :guilabel:`Done`. The employee record is archived, an email with a
download link to their personal documents is sent to the employee's private email address (if
selected), and a red :guilabel:`Archived` banner appears in the top-right corner of the employee
form. The chatter logs the :guilabel:`Departure Date` and :guilabel:`Departure Reason`, and if an
access link was emailed.

.. image:: offboarding/termination.png
   :align: center
   :alt: The employee termination form with all fields filled out.

Send HR documents access link
-----------------------------

If the access link was not sent when first archiving the employee on the :guilabel:`Employee
Termination` form, it can be sent after the employee is archived at any point.

After an employee is archived, they are no longer visible on the main **Employees** app dashboard.
To view the archived employees, click the :icon:`fa-caret-down` :guilabel:`(down arrow)` in the
search bar to reveal a drop-down menu. Click :guilabel:`Archived`, towards the bottom of the
:icon:`fa-filter` :guilabel:`Filters` column, then click off the pop-up window to close it.

Now only archived employees appear on the dashboard. Click on the desired employee to open their
employee form. On this form, click the :icon:`fa-gear` :guilabel:`(gear)` icon in the top-left
corner, then click :guilabel:`Send HR Documents Access Link` from the resulting drop-down menu. The
chatter logs that the link was sent.
