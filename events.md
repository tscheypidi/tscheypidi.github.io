---
layout: page
title: Events
description: Infos about upcoming events
image: assets/images/generic/pic04.jpg
nav-menu: true
---

<link href='https://cdn.jsdelivr.net/npm/fullcalendar@5.3.1/main.min.css' rel='stylesheet' />
<script src='https://cdn.jsdelivr.net/npm/fullcalendar@5.3.1/main.min.js'></script>


<script>

  document.addEventListener('DOMContentLoaded', function() {
    var initialTimeZone = 'local';
    var timeZoneSelectorEl = document.getElementById('timezone-selector');
    var loadingEl = document.getElementById('loading');
    var calendarEl = document.getElementById('calendar');

    var calendar = new FullCalendar.Calendar(calendarEl, {
      timeZone: initialTimeZone,
      contentHeight: 700,
      initialView: 'listYear',
      initialDate: '2022-03-04',
      headerToolbar: '',
      navLinks: true, // can click day/week names to navigate views
      editable: false,
      selectable: true,
      eventLimit: true,
      events: "assets/calendar-events.htm",
      eventTimeFormat: { hour: 'numeric', minute: '2-digit', timeZoneName: 'short' },
    });

    calendar.render();

    // when the timezone selector changes, dynamically change the calendar option
    timeZoneSelectorEl.addEventListener('change', function() {
      calendar.setOption('timeZone', this.value);
    });
  });

</script>

# Calendar

<div style="padding:0 0 15px 0;">
    Timezone:
    <select id='timezone-selector'>
      <option value='local'>local</option>
      <option value='UTC'>UTC</option>
    </select>
</div>


<div id="calendar"></div>


<iframe src="https://cloud.pik-potsdam.de/index.php/apps/calendar/p/3a2r4TYXw7es7ZGt/dayGridMonth/now" title="MAgPIE community calendar"></iframe>