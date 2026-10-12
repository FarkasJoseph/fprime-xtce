# fprime-to-xtce

Convert F-Prime command and telemetry dictionaries into XTCE format. This function creates the YAMCS dialect of XTCE.

## Installation

```bash
pip install -e .
```

## Run

To run this conversion script, use the following:

```bash
fprime-to-xtce </path/to/fprime/dictionary> -o </path/to/xtce/dictionary>
```

## Events

F Prime events are a construct above XTCE containers. These are represented and handled in the the consuming tools (e.g. YAMCS).

## Command Completion

The command dispatcher emits an `OpCodeDispatched` event when it dispatches a command, and it reports the end of each command it dispatches with an `OpCodeCompleted` or `OpCodeError` event. All three events carry the command's opcode. The generated XTCE decodes these events and gives every command a verifier for each, so the YAMCS command history reports three stages: Dispatched, and then Complete or Failed. A failed command also gets a message with the response code that the `OpCodeError` event carries, for example `EXECUTION_ERROR`.

YAMCS waits for each stage without a practical time limit, because how long a command may take depends on the project. The XTCE schema requires a time limit on every verifier, so the generated XTCE sets each one to one year from the moment YAMCS releases the command for sending. Four limitations apply:

- If the same command is sent again before the first one ends, the first event to arrive completes or fails both of them, because the event identifies a command only by its opcode. A command that never reaches the flight software stays waiting, so the event of the next command with the same opcode ends it as well.
- The command dispatcher rejects some commands before it dispatches them, with events such as `InvalidCommand`. No verifier watches those events, so YAMCS keeps waiting for such a command.
- If the F Prime build sets `IncludeCommandOpcodesInEvents` to false in `CommandDispatcherImplCfg.hpp` (it is true by default), the command dispatcher puts a placeholder in place of the opcode in the events that the verifiers watch, so no command is ever marked dispatched, completed or failed.
- YAMCS 5.12.8 keeps every sent command in memory until the time limits of its verifiers expire, even after the command has ended. A command without arguments takes about 3 kB, which YAMCS holds until it restarts or the year is over.

## License

Apache 2.0 License - see [LICENSE](LICENSE) for details.
