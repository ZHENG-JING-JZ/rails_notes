---
layout: default
---


1. Accessing rails scope objects:

	```
	['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'].each do |wkd|
	  @class_sessions.send(wkd)
	end
	```

2. Kill rails process in cmd

	```
	lsof -wni tcp:3000
	```
	This will return PID number and other information of the process

	```
	kill -9 PID
	```

3. Use Prawn gem to generate pdf
	Draw a dash line:

	```
	dash(4, :space => 4, :phase => 1)
  	stroke_horizontal_line 0, 500
  	```

  	Change back to solid line:

  	```
  	undash()
  	```

  	then

  	```
  	stroke_horizontal_line 0, 500
  	```
  	
  	Setting dash line will result in dash lines in following tables. Without using `undash()`, the following tables will have dash lines as well.