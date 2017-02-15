### How to connect from Windows Host to Ubuntu guest in VBOX over SSH?.
From VBOX Network settings
  * Select Adapter 1 ,and use NAT Connection
  * Select Advanced/Port forward option
  * Add new portfrowrd rule
    * Name : SSH
    * Protocol : TCP
    * HOST PORT  : 2281
    * Guest Port : 22
   * Make sure SSHD ( Server is running and listening on port 22)
     * If not ,Start SSH sever in Ubuntu 16 : systemctl start sshd
     * sudo lsof  -i -n -P
        * check to see the sshd is listening on port 22
Window Host 
  * Use WinSCP or Putty
  * SSH  to 127.0.0.1 
