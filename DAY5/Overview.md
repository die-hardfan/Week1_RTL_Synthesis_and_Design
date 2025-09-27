## DAY 5
**Aim:**
1.	Dangers of incomplete if statements
2.	Caveats of case statement
3.	for loop and for-generate
4.	Related labs

## DANGERS OF INCOMPLETE IF
When coding the behaviour of a combinational circuit, if-else statements are used to create one or more stages of mux. If the output is not properly assigned for all combinations of the mux select lines, latches are inferred which makes the circuit sequential, which is a blunder.
But there are places where incomplete if statements are used deliberately, where we intend to infer the latch behaviour. 
KEY: Whether to use incomplete if or not, depends on the circuit that is being coded.

## CAVEATS OF CASE STATEMENT
Case statement also creates one or more stages of mux. 
1.	An incomplete case statement, not all combinations of the case constant are defined, no default statement either, creates inferred latches.
2.	Partial assignments for outputs in one (or more) of the cases creates latches even if default statement is properly defined for all outputs.
3.	Overlapping case definitions creates confusion for simulator, often leading to synthesis-simulation mismatch.
IF-ELSE vs CASE
1.	If-else creates a priority logic, since as soon as an if or else condition is satisfied, the conditions below are not checked. This avoids confusion when more that one condition is satisfied.
2.	Case statement doesn’t have a priority logic, no matter whether a case is matched, every case is checked from top to bottom. 
3.	Both of them must be used within ‘always’ block.

## FOR LOOP vs FOR GENERATE
1.	For loop is used to evaluate an expression within ‘always’ block.
2.	For generate is used outside the ‘always’ block for repeated instantiation of hardware/modules.
3.	Both of them use blocking statements, and their sequential execution is taken advantage here.
