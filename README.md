Download Link: https://assignmentchef.com/product/solved-cse5331-project1-rigorous_2pl_protocol
<br>
In this project, you will implement a program that simulates the behavior of the two-phase locking (2PL) protocol for concurrency control. The particular protocol to be implemented will be <strong>rigorous 2PL</strong>, with the <em>wound-wait </em>method for dealing with deadlock. The input to your program will be a file of transaction operations in a particular sequence. Each line has a single transaction operation. The possible operations are: b (begin transaction), r (read item), w (write item), and e (end transaction). Each operation will be followed by a transaction id that is an integer between 1 and 9. For r and w operations, an item name follows between parentheses (item names are single letters from A to Z). Two example are given below. (<u>Important Note:</u> The input sequence will be affected during the simulation – for example, some commands may cause a transaction to be blocked (wait). In such cases, the simulation should keep track of all operations of this transaction, and if the transaction is resumed, these operations should be simulated).




<u>Examples of two input files:</u>

b1;                                                       b1;

r1 (Y);                                                  r1(Y);

w1 (Y);                                                w1(Y);

r1 (Z);                                                  r1(Z);

b3;                                                       b2;

r3 (X);                                                  r2(Y);

w3 (X);                                                b3;

w1 (Z);                                                 r3(Z);

e1;                                                       w1(Z);

r3 (Y);                                                  e1;

b2;                                                       w3(Z);

r2 (Z);                                                  e3;

w2 (Z);

w3 (Y);

e3;

r2 (X);

w2 (X);

e2;




Your simulation should assign transaction timestamps using an incremental counter, based on the transactions start order, and are integer numbers: 1, 2, 3, …. For example, in the first example input file (on left above), TS(T1) = 1, TS(T3) = 2, TS(T2) = 3, because T1starts first, then T3, then T2. You should do the following steps for Project 1:

<ol>

 <li>Design and implement appropriate data structures to keep track of transactions (<strong>transaction table</strong>) and locks (<strong>lock table</strong>), as well as any other needed information (it is your responsibility as part of the project to determine any additional needed information).</li>

 <li>In the <strong>transaction table</strong>, you should keep relevant information about each transaction. This includes transaction id, transaction timestamp, transaction state (active, blocked (waiting), aborted (cancelled), committed, etc.), list of items locked by the transaction, plus <em>any other relevant information</em>. (It is part of your work to determine and specify other relevant information needed.) For blocked transaction, you should also keep an ordered list of the operations of that transaction (from the input file) that are waiting to be executed once the transaction can be resumed.</li>

 <li>In the <strong>lock table</strong>, you should keep relevant information about each locked data item. This includes item name, lock state (read (share) locked, or write (exclusive) locked), transaction id for the transaction holding the lock (for write locked) or list of transaction ids for the transactions holding the lock (for read locked), list of transaction ids for transactions waiting for the item to be unlocked (if any), plus <em>any other relevant information</em>. (It is part of your work to determine and specify other relevant information.)</li>

 <li>Write a program that reads the operations from an input schedule and simulates the appropriate actions for each operation by referring to and updating the entries in the transaction table and lock table. Your program should <strong>print a short summary of the simulation action</strong> that the program takes to simulate each command, including information on any updates to the system tables (transaction table and lock table), and if the simulation will commit or abort or block a transaction, or just allow the operation to execute.</li>

</ol>

Some basic information about the actions that your program should take (this list is <em>not</em> an exhaustive list):

<ol start="5">

 <li>A transaction record should be created in the transaction table when a <strong>begin transaction operation (b) </strong>is processed. The state of this transaction should be set to <em>active</em> when it begins. A transaction timestamp is created for the transaction and stored with the transaction record.</li>

 <li>Before processing a <strong>read operation (r(X)),</strong> the appropriate <strong>read lock(X)</strong> request should be simulated by your program simulation, and the lock table should be updated appropriately. If the item is not locked, the operation is allowed and the lock table is updated to indicate that the requested item is now read-locked by the transaction.</li>

</ol>

<ol>

 <li>If the item X is already locked by a conflicting write lock, the transaction is either: (i) blocked (<em>wait</em>) and its transaction state is changed to blocked (in the transaction table), or (ii) the simulation will abort the other transaction holding the write lock on item X (<em>wound</em>) and the state of that other transaction is changed to aborted. The deadlock prevention protocol <strong>(<em>wound-wait</em>)</strong> determines which action is taken.</li>

 <li>If the item is already locked by a non-conflicting read lock, the transaction is added to the list of transactions that hold the read lock on the requested item (in the lock table).</li>

</ol>

<ol start="7">

 <li>Before processing a <strong>write operation (w(X)),</strong> the appropriate <strong>write lock(X)</strong> request should be processed by your program simulation (lock upgrading is permitted if the upgrade conditions are met – that is, if the item is read locked by only the transaction that is requesting the write lock). The lock table should be updated appropriately.</li>

</ol>

<ol>

 <li>If the item is not locked, the operation is allowed and the lock table is updated to indicate that the requested item is now write-locked by the transaction.</li>

 <li>If the item is already locked by a conflicting read or write lock, the transaction is either: (i) blocked (<em>wait</em>) and its state is changed to blocked (in the transaction table), or (ii) the simulation will abort the other transaction holding the read or write lock on item X (<em>wound</em>) and the state of that other transaction is changed to aborted. The deadlock prevention protocol <strong>(<em>wound-wait</em>)</strong> determines which action is taken.</li>

</ol>

<ol start="8">

 <li>Your simulation program should include code <strong>for each operation needed</strong>. In addition to writing the code for each operation that can appear in the schedule <strong>b(i), r(i, X), w(i, X), e(i),</strong> you should also write code for the <strong>following additional operations</strong> that will be needed by your simulation and can be called from other procedures in your code: <strong>readlock(i, X), writelock(i, X), unlock(i, X), commit(i), and abort(i).</strong></li>

 <li>Before processing an operation in the input list, you should check if the transaction is in a blocked state or aborted state. (i) If it is blocked, add the operation to the ordered list of the operations of that transaction that are waiting to be executed (in the transaction table); these operations will be executed once the transaction is resumed, subject to the rules of the locking protocol. (ii) If the transaction has already been aborted, its subsequent operations in the input list are ignored.</li>

 <li>Before changing a transaction state to blocked, your program should check the <em>deadlock prevention</em> protocol (<em>wound-wait</em>) rules to determine if the transaction should wait (be blocked) or abort the other transaction (wound). The transaction timestamps are used to decide on which action to take.</li>

 <li>The process of <strong>aborting a transaction</strong> should release (unlock) any items that are currently locked by the transaction, one at a time, and changing the transaction state to aborted in the transaction table. Any subsequent operations of the aborted transaction that are read from the input file should be ignored by the simulation.</li>

 <li>If a transaction reaches its end (e) operation successfully, it should be <strong>committed</strong>. The process of committing a transaction should release (unlock) any items that are currently locked by the transaction, one at a time, and changing the transaction state to committed in the transaction table.</li>

 <li>The process of <strong>unlocking an item</strong> should check if any transaction(s) are blocked because of waiting for the item. If any transactions are waiting, it should remove the first waiting transaction and grant it access to the item (note that this will relock the item) and thus resume the transaction. All waiting operations of the transaction are processed (as discussed above, subject to the locking protocol) before any further operations from the input file are processed.</li>

</ol>