At work developers are not allowed to have access to production databases for reasons.  So there would be a problem
reported to support and they would reach out to development who would determine it was a data issue and needed to see
the data.  Sometimes the developers are on the other side of the world so communicating back and forth can take over a
day.  They would send devops a script to run and devops would send them the output and if there was any follow up or the
script failed or the output wasn't what was needed then the entire communication loop starts  over.  This would happen
several times through my team's day where each of us would get interrupted to run a select statement and return the
results to a dev.  It wasn't difficult, but it did break the flow of whatever you were doing at the time.

I finally had enough and went to my manager with the idea of building a website that would allow anyone to request
access to a production database for a certain amount of time.  The system would then create a user for that access
request in the client database with only select privileges.  After the time expired the system would remove the user
from the client database.  My manager told me it sounded like a great idea and go forth.

This is when I realized I needed to learn how to do frontend development.  So after much AI usage I got a frontend
written in Vue.js.  I also had to fully learn and understand about authentication and jwt tokens.

I created the backend in python using flask.  This was a pretty easy and straight forward task.

I finally finished the website and let people start using it and they love it and there haven't been any complaints
