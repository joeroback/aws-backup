TODO
====
- use py-filelock to allow only one backup concurrently, add option to enable this (e.g. --exclusive)
- add error checking to mysql subprocess
- add error checking to mongo subprocess, also check returned JSON output
- add error checking to fsfreeze subprocess
- once error checking is in place, use 'with' statement to implement rollback on error
