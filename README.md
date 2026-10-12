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

The generated XTCE gives every command verifiers that watch the command dispatcher's events, so YAMCS reports when a command is dispatched and whether it completed or failed. A failed command shows the response code from `OpCodeError`, such as `EXECUTION_ERROR`. These events identify a command only by its opcode, so the next command with the same opcode can end one that is still waiting.

## License

Apache 2.0 License - see [LICENSE](LICENSE) for details.
