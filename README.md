# launderx
from fastapi import FastAPI, Depends, HTTPException, status
from pydantic import BaseModel
from typing import Optional
import random
import string
import hashlib
import time
from datetime import datetime
import sqlite3

app = FastAPI()

# Simulated Offshore Bank
offshore_banks = ["Luxembourg Bank", "Cayman Trust", "Singapore Financial"]

# Simulated Blockchain
blockchain = []

# Simulated User Accounts
users = []

# Simulated Transaction History
transactions = []

class User(BaseModel):
    user_id: int
    name: str
    email: str
    password: str
    balance: float
    is_offshore: bool

class Transaction(BaseModel):
    tx_id: str
    from_user: int
    to_user: int
    amount: float
    currency: str
    timestamp: str
    is_encrypted: bool

class CryptoTransaction(BaseModel):
    tx_id: str
    from_wallet: str
    to_wallet: str
    amount: float
    currency: str
    timestamp: str
    is_mixed: bool

@app.post("/register")
def register_user(user: User):
    user_id = random.randint(1000, 9999)
    user.balance = 100000.0
    user.is_offshore = True
    users.append(user)
    return {"message": "User registered", "user_id": user_id}

@app.post("/deposit")
def deposit_funds(user_id: int, amount: float, currency: str):
    for user in users:
        if user.user_id == user_id:
            user.balance += amount
            return {"message": "Funds deposited", "balance": user.balance}
    raise HTTPException(status_code=404, detail="User not found")

@app.post("/convert_to_crypto")
def convert_to_crypto(user_id: int, amount: float, currency: str):
    tx_id = ''.join(random.choices(string.ascii_letters + string.digits, k=10))
    tx = CryptoTransaction(
        tx_id=tx_id,
        from_wallet=user_id,
        to_wallet=random.randint(1000, 9999),
        amount=amount,
        currency=currency,
        timestamp=str(time.time()),
        is_mixed=True
    )
    blockchain.append(tx)
    return {"message": "Converted to crypto", "tx_id": tx_id}

@app.post("/transfer_offshore")
def transfer_offshore(user_id: int, amount: float, currency: str):
    tx_id = ''.join(random.choices(string.ascii_letters + string.digits, k=10))
    tx = Transaction(
        tx_id=tx_id,
        from_user=user_id,
        to_user=random.randint(1000, 9999),
        amount=amount,
        currency=currency,
        timestamp=str(time.time()),
        is_encrypted=True
    )
    transactions.append(tx)
    return {"message": "Transferred offshore", "tx_id": tx_id}

@app.get("/view_transactions")
def view_transactions():
    return {"transactions": transactions}

@app.get("/view_blockchain")
def view_blockchain():
    return {"blockchain": blockchain}
