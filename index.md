---
layout: default
---


1. Accessing rails scope objects:

```
['monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday', 'sunday'].each do |wkd|
  @class_sessions.send(wkd)
end
```