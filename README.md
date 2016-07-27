# HTTP-DAV-Nginx
Perl Client library for Nginx WebDAV server

#Description
NGINX "supports" WebDAV by means of a module, but this support is incomplete: 
it only handles the WebDAV methods PUT, DELETE, MKCOL, COPY, and MOVE, 
and leaves the necessary OPTIONS and PROPFIND (and the optional LOCK, UNLOCK, and PROPPATCH) 
methods unimplemented.

This module doesn't uses PROPFIND and OPTIONS commands for work.


##Installation
```shell
perl Makefile.PL
make
make test
make install

#or 

cpan -i HTTP::DAV::Nginx
```

##Example
```perl
use HTTP::DAV::Nginx;

$dav = HTTP::DAV::Nginx->new('http://host.org:8080/dav/');
or
$dav = HTTP::DAV::Nginx->new('http://host.org:8080/dav/', die_on_errors => 1);

unless ($dav->mkcol('/test'))
{
  print "ERROR:" . $dav->err;
}

$dav->put('/test/123', data => 'Hello');

$dav->copy('/test/123', '/test2/12345');

$dav->move('/test/123', '/test3/123456');

$dav->delete('/test2/12345');

$ua = $dav->useragent;
```


##Methods

###new(URI, [PARAMS])
Create a new HTTP::DAV::Nginx object;
```perl
my $dav = HTTP::DAV::Nginx->new('http://host.org:8080/dav/');
or 
HTTP::DAV::Nginx->new('http://host.org:8080/dav/', die_on_errors => 1);
```
PARAMS:
* die_on_errors - die if error
* warn_on_errors - warn if error

### mkcol(URI)
creates a new dir at the location specified by the URI


### copy(SRC_URI, DEST_URI, [PARAMS])
creates a duplicate of the source resource identified by URI
```perl
$dav->copy('/uri', '/uri2');
```
SRC_URI - source URI

DEST_URI - destination URI

PARAMS:
* depth - copy depth (0, infinity)
* owerwrite - overwrite existing files (1 - overwrite, 0 - don't)

### move(SRC_URI, DEST_URI, [PARAMS])
used to move a resource to the location specified by a URI
```perl
$dav->move('/uri', '/uri2');
$dav->move('/uri', '/uri2', overwrite => 1);
```

SRC_URI - source URI

DEST_URI - destination URI

PARAMS:
* depth - move depth (0, infinity)
* overwrite - overwrite existing files (1 - overwrite, 0 - don't)


### put(URI, DATA_TYPE => DATA)
used to put data in a new resource specified by a URI
```perl
$dav->put('/uri', data => 'Hello');
$dav->put('/uri', file => '/etc/passwd');
$dav->put('/uri', fh => $fh);
```

DATA_TYPE - type of data:
* data - scalar data
* file - filename
* fh - filehandle

DATA - scalar data or filename or filehandle


### delete(URI, [PARAMS])
deletes a resource at the specified
```perl
$dav->delete('/uri');
$dav->delete('uri', depth => 'infinity');
```

### useragent
return LWP::UserAgent object for custom options (i.e. proxy, cookie etc)


###err
return last error string