
###Ftrace trace-cmd setup to capture the network flow.

 1. List all trace options
	trace-cmd list
   
 2. Check the function do_IRQ is availble for tracing
	trace-cmd list -f | grep do_IRQ

 3. Adde the events -e : subsystems irq and net and add functions -l do_IRQ.Use -p option to enable the function tracer.
     	 trace-cmd record -p function -e irq -e net -l do_IRQ

 4. View the captured events using 
	 trace-cmd report

 5. To genrate function graph : -p function_graph
         trace-cmd record -p function_graph -e irq -e net -l do_IRQ  -l netif_receive_skb
   	 trace-cmd report

 6. Check supported network functions avaialble.
         trace-cmd list -f | grep netif
   
 7. View the .dat file saved using kernelshark trace.dat 


