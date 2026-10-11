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

## Algorithm 

### Assumptions/Conventions
- The compressor (or other sensed system) is identified as the "controlling" load. 
- The valve (or other driven system) is identified as the "controlled" load.
- The operation of the system is controlled by a state machine. 

### State Machine

| State (name)   | Stopped  | Running | Timeout |
| -------------- | -------- | ------- | ------- |
| S0 (Idle)      | S0       | S1      | S0      |
| S1 (Armed)     | S2       | S1      | S0      |
| S2 (Triggered) | S2       | S1      | S3      | 
| S3 (Drain)     | S0,S4(1) | S1      | S0      |
| S4 (Followup)  | S4       | S1      | S5      |
| S5 (Drain2)    | S0       | S1      | S0      |

- S0: Represents the idle state, where the system is waiting on the detection of the 
  controlling load to be turned on.  This is the initial state the system enters 
  after power on or a watchdog reset.
- S1: Represents the "armed" state where the controlling load has been detected to 
  be running.  As long as the controlling load is running, the system will be in this
  state. The armed state is entered from any state when the controlling load is 
  running. 
- S2: Represents the triggered state after the controlling load has stopped running
  and represents the start of the initial countdown period to operate the drain. If 
  the controlling load does not restart before the countdown timer expires, the 
  system will transition to the draining state. 
- S3: Represents the "draining" state.  This is where the drain valve is opened 
  for one second to drain the condensant water from the tank (or other operation 
  depending on application). Once the draining time has expired, the system will 
  transition to either idle or followup state (S0 or S4, see Note 1). 
- S4: This represents the followup countdown interval.  It is entered only after 
  a successfull drain cycle and  the followup  time intervals are selected by the 
  option switches.
- S5: Represents the followup drain cycle, which is initiated some time interval
  after a successful initial drain cycle if selected by the option switches. 

### Notes
1. Either S0 (Idle) or S4 (Followup) states are entered after an initial 
   drain cycle has occurred based on the setting of the option switches.  If the 
   option switches indicate no followup drain cycle, then the machine transitions
   to the idle (S0) state.  If the option switches indicate a followup drain 
   interval, the transition is to the S4 state and the followup countdown 
   interval timer is started.
2. If the controlling load is restarted at any time, any current countdown 
   interval is stopped and the state machine immediately enters the Armed 
   (s1) state.

### Option Switches: 
The option switches are a 4-position DIP switch divided into two 2-bit groups.
The first group (bits 0..1) represent the triggered countdown interval, and are
specified as: 
- 00: Cycle immediately after turn off.  There is no triggered delay, and 
  the state machine immediately cycles to the Drain (S3) state. 
- 01: The system will remain in the triggered state for 10 minutes, then 
  transition to the Drain (s3) state.
- 10: The system will remain in the triggered state for 30 minutes, then 
  transition to the Drain (S3) state. 
- 11: The system will remain in the triggered state for 1 hour (60 minutes), 
  then transition to the Drain (S3) state;

The second pair of bits (2..3) indicate the followup drain cycle, if any. 
- 00: No follow up cycle is to be used.  Transition to the Idle (S0) state
- 01: An additional followup drain cycle will occur 1 hour after the drain cycle.
- 10: An additional followup drain cycle will occur 2 hours after the drain cycle.
- 11: An additional followup drain cycle will occur 3 hours after the drain cycle.


