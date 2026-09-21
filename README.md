import os


RPC_URL = os.getenv("RPC_URL", "https://eth.llamarpc.com")
TOKEN_ADDRESS = os.getenv("TOKEN_ADDRESS")

w3 = Web3(Web3.HTTPProvider(RPC_URL))

if not w3.is_connected():
    raise ConnectionError("Failed to connect to RPC.")

if not TOKEN_ADDRESS:
    raise ValueError("Set TOKEN_ADDRESS first.")

token_address = Web3.to_checksum_address(TOKEN_ADDRESS)

ERC20_ABI = [
    {
        "constant": True,
        "inputs": [],
        "name": "name",
        "outputs": [{"name": "", "type": "string"}],
        "type": "function",
    },
    {
        "constant": True,
        "inputs": [],
        "name": "symbol",
        "outputs": [{"name": "", "type": "string"}],
        "type": "function",
    },
    {
        "constant": True,
        "inputs": [],
        "name": "decimals",
        "outputs": [{"name": "", "type": "uint8"}],
        "type": "function",
    },
    {
        "constant": True,
        "inputs": [],
        "name": "totalSupply",
        "outputs": [{"name": "", "type": "uint256"}],
        "type": "function",
    },
]

token = w3.eth.contract(
    address=token_address,
    abi=ERC20_ABI
)

try:
    name = token.functions.name().call()
    symbol = token.functions.symbol().call()
    decimals = token.functions.decimals().call()
    supply = token.functions.totalSupply().call()

    formatted_supply = supply / (10 ** decimals)

    print("=" * 60)
    print("ERC20 TOKEN INSPECTOR")
    print("=" * 60)
    print(f"Name:          {name}")
    print(f"Symbol:        {symbol}")
    print(f"Decimals:      {decimals}")
    print(f"Total Supply:  {formatted_supply:,.4f}")
    print(f"Contract:      {token_address}")
    print(f"Chain ID:      {w3.eth.chain_id}")
    print("=" * 60)

except Exception as error:
    print(f"Unable to inspect token: {error}")
