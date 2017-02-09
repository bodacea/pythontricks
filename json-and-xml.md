# JSON and XML

import json

fout = open\(filename, 'w'\)

json.dump\(dataset, fout\)



### Prettyprint json

def printkids\(heading, parent\):

    for kid in parent.keys\(\): 

        if type\(parent\[kid\]\) == dict:

            print\('{} {}'.format\(heading, kid\)\)

            printkids\(heading + '--', parent\[kid\]\)

        else:

            print\('{} {}: {}'.format\(heading, kid, parent\[kid\]\)\)

    return





