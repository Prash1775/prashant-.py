import streamlit as st
import hashlib
import time

# Class Definitions
class GymMemberEntry:
    def __init__(self, member_name, membership_type, previous_hash=''):
        self.member_name = member_name
        self.membership_type = membership_type
        self.timestamp = time.time()
        self.previous_hash = previous_hash
        self.hash = self.compute_hash()

    def compute_hash(self):
        entry_string = f"{self.member_name}{self.membership_type}{self.timestamp}{self.previous_hash}"
        return hashlib.sha256(entry_string.encode()).hexdigest()

    def to_dict(self):
        return {
            "Member": self.member_name,
            "Membership Type": self.membership_type,
            "Timestamp": time.ctime(self.timestamp),
            "Hash": self.hash,
            "Previous Hash": self.previous_hash
        }

class GymLedger:
    def __init__(self):
        if "ledger_chain" not in st.session_state:
            st.session_state.ledger_chain = []
            self.create_genesis_entry()
        self.chain = st.session_state.ledger_chain

    def create_genesis_entry(self):
        genesis_entry = GymMemberEntry("Genesis", "None", "0")
        st.session_state.ledger_chain.append(genesis_entry)

    def add_entry(self, member_name, membership_type):
        last_hash = self.chain[-1].hash
        new_entry = GymMemberEntry(member_name, membership_type, last_hash)
        self.chain.append(new_entry)

    def display_ledger(self):
        return [entry.to_dict() for entry in self.chain]

    def verify_integrity(self):
        for i in range(1, len(self.chain)):
            current = self.chain[i]
            prev = self.chain[i - 1]
            if current.previous_hash != prev.hash:
                return False, i
        return True, -1

# --- Streamlit App ---
st.set_page_config(page_title="Gym Membership Ledger", layout="wide")
st.title("🏋️‍♂️ Gym Membership Ledger using Hashing")

ledger = GymLedger()

# Input Form
with st.form("entry_form"):
    member_name = st.text_input("Member Name")
    membership_type = st.selectbox("Membership Type", ["Monthly", "Weekly", "Annual"])
    submitted = st.form_submit_button("Add Member")
    if submitted:
        if member_name.strip():
            ledger.add_entry(member_name, membership_type)
            st.success(f"Added member: {member_name} ({membership_type})")
        else:
            st.error("Please enter a valid name.")

# Display Ledger
st.subheader("📒 Ledger Entries")
ledger_data = ledger.display_ledger()
st.dataframe(ledger_data)

# Verify Integrity
if st.button("🔍 Verify Ledger Integrity"):
    is_valid, broken_index = ledger.verify_integrity()
    if is_valid:
        st.success("✅ Ledger integrity verified. No tampering detected.")
    else:
        st.error(f"❌ Ledger integrity broken at entry {broken_index}")
