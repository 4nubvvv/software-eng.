# ariane5_demo.py - simplified model of the Ariane 5 Flight 501 conversion error
INT16_MIN, INT16_MAX = -32768, 32767

def convert_unprotected(value):
    """No range check: result silently wraps around (corrupted data)."""
    v = int(value) & 0xFFFF
    return v - 0x10000 if v > 0x7FFF else v

def convert_checked(value):
    """Range check: raises an exception, like Ada's Operand Error."""
    v = int(value)
    if not (INT16_MIN <= v <= INT16_MAX):
        raise OverflowError(f"Operand Error: {v} does not fit in 16 bits")
    return v

# Illustrative horizontal bias (BH) values, 64-bit float
bh_ariane4 = 20000.0   # smaller value, Ariane 4 trajectory
bh_ariane5 = 64000.0   # larger value, Ariane 5 trajectory

for name, bh in [("Ariane 4", bh_ariane4), ("Ariane 5", bh_ariane5)]:
    print(f"--- {name}: BH = {bh}")
    print("  unprotected ->", convert_unprotected(bh))
    try:
        print("  checked     ->", convert_checked(bh))
    except OverflowError as err:
        print("  checked     -> EXCEPTION:", err)
