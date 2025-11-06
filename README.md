# Canvas Teacher Bulk Remove Enrollments Tool
A Tampermonkey/Greasemonkey userscript that adds a UI in Canvas People page to bulk remove Teacher enrollments from a course.
> Tested on Canvas UI with built-in jQuery UI. Uses Canvas REST endpoints to fetch sections/enrollments and send batched delete requests.

## Features
<ul>
  <li>Adds a “Remove Bulk Teacher Enrollments” button on People (/courses/:id/users)</li>
  <li>Lists all Teacher enrollments with:</li>
  <ul>
    <li>Teacher Name</li>
    <li>Section</li>
    <li>Enrollment Date</li>
    <li>Last Activity (sortable columns)</li>
  </ul>
  <li>Select all/multi-select via checkboxes</li>
  <li>Batcher deletion (Chuncks of 40, ~5s pause) to be gentle on rate limits</li>
  <li>Sucess and progress dialogs with simple feedback</li>
  <li>Role-gated: button shows only for user with any of teacher, limited_admin, admin, root_admin</li>
</ul>

## Installation 
<ol>
  <li>Install a userscript manager:</li>
  <ul>
    <li>
      Tampermonkey (recommended)
    </li>
    <li>
      Greasemonkey/Violentmonkey also work
    </li>
  </ul>
  <li>Create a new script and paste the contents of canvas-teacher-bulk-remove.user.js</li>
  <li>Update the domain include (if needed) to match your Canvas hostname(s)</li>
</ol>

Metadata block example (from this repo):
```bash
// ==UserScript==
// @name         Canvas Teacher Bulk Remove Enrollments Tool
// @namespace    https://github.com/sukotsuchido/CanvasUserScripts
// @version      2.11
// @description  A Canvas UserScript to bulk remove teacher enrollments from a course.
// @author       Morgan H. McKie (m.mckie123@gmail.com)
// @include      https://dev.instructure.com/courses/*/users
// @require      https://code.jquery.com/jquery-3.4.1.min.js
// @grant        none
// ==/UserScript==
```

If you want the script to run in multiple environments (prod/test/beta), replace @include with @match lines, for example:
```bash
// @match  https://*.instructure.com/courses/*/users
// @match  https://canvas.*.edu/courses/*/users
```

> Only users with the proper Canvas permissions will be able to remove enrollments.

## Usage

<ol>
  <li>Navigate to Course → People (/courses/:course_id/users).</li>

  <li>If your role includes teacher, limited_admin, admin, or root_admin, you’ll see a button:</li>
    <ol> <li> Remove Bulk Teacher Enrollments.</li></ol>
  <li>Click the button to open the dialog:</li>

  <ol>
    <li>Use the header checkbox to Select All or pick individually.</li>
    <li>Click Remove Teachers to begin.</li>
  </ol>

  <li>The script deletes in batches of 40 enrollments with short waits in between.</li>

  <li>On success, you’ll see a confirmation; the page refreshes to reflect changes.</li>

</ol>
## How it works (high level)

<ul>
  <li>Sections: GET /api/v1/courses/:course_id/sections?per_page=99</li>

  <li>Enrollments (teachers): GET /api/v1/courses/:course_id/enrollments?type[]=TeacherEnrollment&per_page=99&page=… (Follows Link headers to collect all pages)</li>

  <li>Delete: DELETE /api/v1/courses/:course_id/enrollments/:enrollment_id?task=delete (Executed asynchronously in small batches)</li>
</ul>


The UI uses Canvas’ styling classes and jQuery UI dialogs already present in Canvas.
