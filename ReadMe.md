# Air Compressor Automatic Drain
This project is an adaptive air compressor automatic drain system.  There are examples of products
on the market that use a timer to cycle a solenoid valve on the tank drain to release any moisture
that has condensed at the bottom of the tank.  The problem with these devices is that they cycle 
periodically (such as every 10 minutes) wether they need to or not.  For compressors that run only
sporadically, such as in a home shop, this causes aire loss and needless extra cycling of the compressor.

What this project does is sense when the compressor runs, then start a timer after it shuts off.
After some period of time (to allow condensant to accumulate), the drain valve is cycled.  The 
drain valve is then cycled again 1 hour after the last run to drain any additional accumulated 
condenstant.  After that, the drain valve is not cycled again until the compressor is run 
again.  This allows the system to efficiently drain the condensant water without needlessly 
cycling the drain valve.