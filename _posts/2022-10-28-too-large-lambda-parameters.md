I recently put all the values for services in one giant dictionary and tried to pass it to Lambda as an environment variable
and it failed due to a size limitation.  I googled around and from this github post I got the idea to base64 encode the compressed
string and then unencode it in the lambda (because I didn't want to deal with adding it as a layer).

Create encoded constants:

`encoded_constants = base64.urlsafe_b64encode(zlib.compress(json.dumps(constants).encode('ascii')))`

Pass in environment dictionary:

`encoded_constants=encoded_constants.decode('ascii')`

Then unencode, decompress in lambda:

`products = json.loads(zlib.decompress(base64.urlsafe_b64decode(os.getenv('encoded_constants'))))`
