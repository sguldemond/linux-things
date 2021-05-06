List file and folder sizes
```
# du -hs ./*
```

Create SSH keypair
```
$ ssh-keygen -o -b 4096 -t rsa
```

Open SSH tunnel
```
$ ssh foobar@83.96.239.246 (-N) -L localhost:8083:83.96.239.246:8083
```

Create new user
```
# adduser foobar
```

Add user to group
```
# usermod -aG $GROUP $USER
```

Change ownership of folder
```
# chown (-R) foo:bar $FOLDER
```

Find files/folders and get their size
```
$ find ~ -name "venv" | xargs -n 1 du -hs
```