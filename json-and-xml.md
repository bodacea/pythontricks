# JSON and XML

### Write json file \(1\)

import json

fout = open\(filename, 'w'\)

json.dump\(dataset, fout\)

### Write json file \(2\)

If the format's a bit hinky \(e.g. array of json objects\), try pickle...

import pickle 

fout = open\(filename, 'wb'\)

pickle.dump\(dataset, fout\) \# get back with pickle.load\(\)

fout.close\(\)

### Prettyprint json

def printkids\(heading, parent\):

```
for kid in parent.keys\(\): 

    if type\(parent\[kid\]\) == dict:

        print\('{} {}'.format\(heading, kid\)\)

        printkids\(heading + '--', parent\[kid\]\)

    else:

        print\('{} {}: {}'.format\(heading, kid, parent\[kid\]\)\)

return
```



