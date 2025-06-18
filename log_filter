import cantools
import os


dbc_path = "xxx.dbc"
db = cantools.database.load_file(dbc_path)

as_ecu_ids = set()

for msg in db.messages:
    if msg.senders and "as_ecu" in msg.senders:
        as_ecu_ids.add(msg.frame_id)

as_ecu_ids_hex = [f"{msg_id:03X}" for msg_id in as_ecu_ids]
as_ecu_ids_hex.sort()

print("AS_ECU Frame IDs (hex):")
for msg_id in as_ecu_ids_hex:
    print(msg_id)


log_candidates = [f for f in os.listdir('.') if f.endswith('.log') or f.endswith('.txt')]
if len(log_candidates) == 0:
    raise FileNotFoundError("no log file found")
elif len(log_candidates) > 1:
    raise ValueError(f"multi log file")
else:
    log_in_path = log_candidates[0]
    print(f"[INFO] Using log file: {log_in_path}")
base, ext = os.path.splitext(log_in_path)
log_out_path = f"{base}_filtered{ext}"
print(f"[INFO] Output will be saved to: {log_out_path}")

removed = 0
total = 0
as_ecu_ids_str = set(as_ecu_ids_hex)
with open(log_in_path, "r") as fin, open(log_out_path, "w") as fout:
    for line in fin:
        total += 1
        parts = line.strip().split()
        if len(parts) >= 3:
            try:
                can_id = parts[2].split("#")[0].upper()
                if can_id not in as_ecu_ids_str:
                    fout.write(line)
                else:
                    removed += 1
            except Exception:
                fout.write(line)
        else:
            fout.write(line)

print(f"[DONE] Removed {removed} / {total} lines. Output saved to {log_out_path}")
