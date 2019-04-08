## curl
data transfer tool from/to a server
refs: https://www.computerhope.com/unix/curl.htm

## add aliases to create terminal shortcuts
$ vi ~/.bash_profile
#### then add the following lines
alias elasticsearch="/Users/Jasmine.Mou@ibm.com/Applications/elasticsearch-6.4.0/bin/elasticsearch"
#### then save and quit vim `:wq` 
$ source ~/.bash_profile 

## find out the running logstash instance and kill it
$ ps -ef | grep logstash 
# the second number will be the PID
$ kill PID

## kill local tcp port
$ kill $(lsof -t -i :YOUR_PORT_NUMBER)

## curl
refs: https://devhints.io/curl

computer networks: get, post, request
pull, push

