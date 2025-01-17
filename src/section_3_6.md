# Soliciting Witnesses from File


Entering witness values at the command line can be annoying, and placing generated witnesses into the circuit is not always prudent. To avoid this, one may solicit values from a `.json` file. Take the above program from [the last section](section_3_5.md) along with the following file.

```javascript
{ "x": "5",
  "y": "2",
  "z": "27",
  "h": "28" }
```

which will be called `pub.json`. During proving, we may issue it via the `-i` (equivalently, `--inputs`) argument.

```bash
$ ./target/debug/vamp-ir plonk prove -i examples/pub.json \
                                     -u examples/params.pp \
                                     -c examples/circuit.plonk \
                                     -o examples/proof.plonk

> * Reading arithmetic circuit...
> * Reading inputs from file: tests/pubt.json
> [...]
> * Proof generation success!
```

This is semantically equivalent to issuing these arguments at the command line. This procedure does not distinguish between public and private variables.

