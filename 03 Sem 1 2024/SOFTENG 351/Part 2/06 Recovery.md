The recovery process restores the database to the most recent consistent state before the time of failure.

### Typical Recovery Strategies

1. **Restore a backed-up copy of the database:** Best in extensive damage.
2. **Identify any changes that may cause inconsistency:** Best in non-catastrophic failure.
3. **Deferred Update:** Do not physically update the database until after the transaction is committed. Undo is not needed; redo may be needed.
4. **Immediate Update:** The database may be updated by some operations of a transaction before it reaches the commit point. 

Undo and redo operations required to be idempotent. 
- Executing operations multiple times is equivalent to executing just once. 
- The entire recovery process should be idempotent.
![[Pasted image 20240530094753.png | center | 300]]

### Undo and Redo Operations

- $\text{undo}(T_i)$: Restores the value of all data items updated by $T_i$ to their old values, going backwards from the last log record for $T_i$.
    - Each time a data item $X$ is restored to its old value $V$, a special log record $<T_i, X, V>$ is written out.
    - When the undo of a transaction is complete, a log record $<T_i \text{ abort}>$ is written out.
- $ \text{redo}(T_i) $: Sets the value of all data items updated by $T_i$ to the new values, going forward from the first log record for $T_i$.
    - No logging is done in this case.

## Recovering from Failure

- A transaction $T_i$ needs to be undone if the log contains the record $<T_i \text{ start}>$ but does not contain either the record $<T_i \text{ commit}>$ or $<T_i \text{ abort}>$.

- A transaction $T_i$ needs to be redone if the log contains the records $<T_i \text{ start}>$ and contains the record $<T_i \text{ commit}>$ or $<T_i \text{ abort}>$.

## Checkpoints

### Checkpoint Process

1. Output all log records currently residing in main memory onto stable storage.
2. Output all modified buffer blocks to the disk.
3. Write a log record $<\text{checkpoint } L>$ onto stable storage where $L$ is a list of all transactions active at the time of checkpoint.
4. All updates are stopped while checkpointing.

### Recovery Using Checkpoints

1. Suspend execution of all transactions temporarily.
2. Force-write all main memory buffers that have been modified to disk.
3. Write a checkpoint record to the log, and force-write the log to the disk.
4. Resume executing transactions.

For recovery, only consider the most recent transaction $T_s$ that started before the checkpoint, and transactions that started after $T_s$. 
- Scan backwards from the end of the log to find the most recent $<\text{checkpoint } L>$ record. 
- Only transactions that are in $L$ or started after the checkpoint need to be redone or undone. 
- Transactions that committed or aborted before the checkpoint already have all their updates output to stable storage.

Some earlier part of the log may be needed for undo operations    
- Continue scanning backwards till a record $<T_{i}, \text{Start}>$ is found for every transaction $T_{i}$ in L.
- Parts of log prior to earliest < $T_{i}$ start> record above are not needed for recovery, and can be erased whenever desired.
![[Pasted image 20240530211753.png | Center | 400]]
## Rollback

- Transaction failure after update but before commit requires rollback.
- Old data values restored using undo-type log entries.
- Cascading rollback caused by dirty read should be avoided.
	- Almost all recovery mechanisms were designed to avoid this

## Recovery Algorithms

### Deferred Update
The transaction cannot change the database on disk until it reaches its commit point.

- Postpone updates to the database on disk until the transaction completes successfully and reaches its commit point.
- Transaction does not reach its commit point until all its REDO-type log entries are recorded in the log and the log buffer is force-written to disk.
- Redo-type log entries are needed.
- Undo-type log entries are not necessary.
- Can only be used for short transactions and transactions that change a few items due to buffer space issues.

#### Example

- Redo the transactions $(T_1, T_2)$ that were committed after the latest checkpoint.
- No undo required.
![[Pasted image 20240530221644.png | center | 400]]
![[Pasted image 20240530223207.png | center | 300]]

### Immediate Update
- Transaction can change the database on disk before its commit point.
- UNDO-type log entries must be stored.
- Recovery algorithms: UNDO/REDO.

#### Examples

1. Undo $T_0$: B is restored to 2000 and A to 1000, and log records $<T_0, B, 2000>$, $<T_0, A, 1000>$, $<T_0 \text{ abort}>$ are written out.
2. Redo $T_0$ and undo $T_1$: A and B are set to 950 and 2050 and C is restored to 700. Log records $<T_1, C, 700>$, $<T_1 \text{ abort}>$ are written out.
3. Redo $T_0$ and redo $T_1$: A and B are set to 950 and 2050 respectively. Then C is set to 600.

![[Pasted image 20240530223446.png | center | 350]]
