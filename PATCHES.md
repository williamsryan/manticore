# Manticore Modifications for Proteus Evaluation

## Build Status

✅ **Successfully installed via pip in Python 3.10 venv**

## Installation

```bash
cd evaluation
python3.10 -m venv .venv
.venv/bin/pip install -e tools/manticore
```

## Patches Applied

### 1. Fixed `wasm` library Python 3.10+ compatibility
**File:** `.venv/lib/python3.10/site-packages/wasm/types.py`
**Issue:** `collections.Callable` deprecated in Python 3.10+
**Fix:** Changed to `collections.abc.Callable`

```bash
sed -i.bak 's/collections\.Callable/collections.abc.Callable/g' \
  .venv/lib/python3.10/site-packages/wasm/types.py
```

### 2. Fixed Ethereum module sha3 dependency
**File:** `manticore/ethereum/abi.py` and `manticore/platforms/evm.py`
**Issue:** `pysha3` won't compile on Python 3.12/ARM64
**Fix:** Added fallback to `hashlib.sha3_256`

## Known Limitations

⚠️ **MacOS Support:** Manticore warns "only supported on Linux" but WASM analysis works with limitations
⚠️ **Workspace Pollution:** Creates `mcore_*` directories (added to gitignore)

## Usage for Proteus Evaluation

Manticore requires Python 3.10 venv (not system Python 3.12).

```python
from manticore.wasm import ManticoreWASM

m = ManticoreWASM('file.wasm')

def arg_gen(state):
    arg = state.new_symbolic_value(32, 'x')
    state.constrain(arg >= 0)
    state.constrain(arg < 10)
    return [arg]

m.invoke('function_name', arg_gen)
m.run()

# Check terminated states
print(f'States: {m.count_terminated_states()}')
```

## Integration Status

🔄 **Testing in progress** - Manticore executes but bug detection needs validation

## Future Contributions

- [ ] Fix MacOS compatibility issues
- [ ] Improve error reporting for WASM
- [ ] Add JSON output format
