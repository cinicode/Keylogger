# Keylogger-C-Unix

<H4>A simple daemon keylogger written in C on Unix platform, that sends keyboard press events to a server or stores them locally.</H5>

<H2> Table of contents </H2>
<ul>
  <li><a href="#Usage">Usage</a></li>
  <ul>
    <li><a href="#Compiling">Compiling</a></li>
    <li><a href="#Arguments">Command line arguments</a></li>
  </ul>
  <li><a href="#How">How does it work?</a></li>
  <ul>
    <li><a href="#Keylogger">Keylogger</a></li>
      <ul>
        <li><a href="#Idea">General idea</a></li>
        <li><a href="#Socket">Socket or regular file</a></li>
        <li><a href="#Finding">Finding keyboard device</a></li>
        <li><a href="#Sending">Sending events</a></li>
        <li><a href="#Signals">Signals handling</a></li>
      </ul>
    <li><a href="#Daemon">Daemon process</a></li>
    <li><a href="#Server">Server</a></li>
  </ul>
  <li><a href="#References">References</a></li>
  <li><a href="#Disclaimer">Disclaimer</a></li>
</ul>

<H2 id="Usage"> Usage </H2>

<H3 id="Compiling"> Compiling </H3>

In order to compile the program, run this command: <code>gcc main.c keylogger.c daemon.c -o daemon-keylogger</code>.

<H3 id="Arguments"> Command line arguments </H3>

You can invoke the executable in two possible ways:
<ul>
  <li>&lt<b>executable_path</b>&gt &lt<b>host</b>&gt &lt<b>port</b>&gt &lt<b>is_single_instance</b>&gt</li>
  <li>&lt<b>executable_path</b>&gt &lt<b>file_out</b>&gt &lt<b>is_single_instance</b>&gt</li>
</ul>
Where:
<ul>
  <li><b>executable_path</b>: path of the executable</li>
  <li><b>host</b> and <b>port</b>: respectively the hostname or ip address and the port of the server you want to send the events to</li>
  <li><b>file_path</b>: path of the regular file you want to store events into</li> 
  <li><b>is_single_instance</b>: 1 if you only want an istance of the daemon running, 0 otherwise</li>
</ul>
Running examples: <code>./daemon-keylogger 127.0.0.1 12345 0</code> or <code>./daemon-keylogger file_out.txt 1</code>.


<H2 id="How"> How does it work? </H2>

<H3 id="Idea"> General idea </H3>
First of all, you have to specify where to send the events, to a server or locally in a regular file, and you also have to choose between allowing multiple instances of the daemon run simultaneously (for example, you may want to send events to multiple servers) or having just one copy of the daemon running. However, i added this option just for practice rather than for a particular useful purpose :stuck_out_tongue_closed_eyes:. If the daemon is single instance, before starting the keylogger module, we have to check if another copy of this daemon is already running, in such case the program simply exits. The program consists of parts:
<ol>
  <li>Daemonize process: In this phase, the process is converted into a daemon, which is a process that doesn't have a controlling terminal, for this reason they run in the background. </li>
  <li>Guarantee single instance: If you specified to have the daemon as single instance, we have to assure that another copy is not already running, in that case we can acquire the lock of a particular file and have only a single instance run</li>
  <li>Find keyboard device:  
    
     

If you choose to send events to a server, a connection with it must be established, if you choose to save events locally, the file has to be opened. 