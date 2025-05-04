## Andromeda ADO Execute and Query Messages Index

**This index provides explanations for each execute and query message found in the dropdown lists of ADOs within the Andromeda web app.**

**Please note that this list may not be exhaustive, as new ADOs and messages are continuously being added to the Andromeda ecosystem.** 

### General Execute Messages (Available to Most ADOs)

*   **Ownership Messages:**
    *   **Update Owner:** Offers ownership of the ADO to a new address.
    *   **Revoke Ownership Offer:** Cancels a pending ownership offer. 
    *   **Accept Ownership:** Accepts a pending ownership offer and becomes the new owner.
    *   **Disown:** Permanently removes ownership from the ADO. 
*   **Update App Contract:** Changes the associated App contract for the ADO, allowing it to reference components within the new App. 
*   **Update Kernel Address:** Modifies the Kernel ADO used by the ADO for AMP messaging. 
*   **Permissioning Messages:** (If permissioning is enabled)
    *   **Permission Action:** Enables permissioning for a specific execute message of the ADO.
    *   **Set Permission:** Assigns permission levels (Blacklisted, Limited, Whitelisted) to a specific address for a particular action.
    *   **Remove Permission:** Revokes a previously set permission for an address and action.
    *   **Disable Action Permissioning:** Disables permissioning for a specific action.

### General Query Messages (Available to Most ADOs)

*   **Owner:** Retrieves the current owner address of the ADO.
*   **Ownership Request:** Returns the address with a pending ownership offer, if any. 
*   **Type:** Fetches the ADO type and version. 
*   **Kernel Address:** Retrieves the contract address of the Kernel ADO used by the ADO.
*   **Original Publisher:** Returns the address of the original creator/publisher of the ADO.
*   **Block Height Upon Creation:** Fetches the block height at which the ADO was created.
*   **Version:** Returns the version of the ADO.
*   **App Contract:** Retrieves the address of the App ADO, if any, that instantiated the ADO.
*   **Balance:** Queries the balance of a specified address for native or CW20 tokens associated with the ADO.
*   **Permissions:**  Returns the permissions assigned to a specific address for various actions of the ADO.
*   **Permissioned Actions:** Retrieves a list of actions (execute messages) that have permissioning enabled.

### Auction ADO

*   **Execute Messages:**
    *   **Receive NFT:** (Internal) Receives an NFT from a CW721 contract and initiates an auction based on provided parameters.
    *   **Start Auction:** Starts an auction for a specified NFT, including setting the start/end time, bidding denom, minimum bid, whitelist, and recipient of funds.
    *   **Authorize Token Contract:** Allows a specific CW721 contract to send NFTs to the Auction ADO.
    *   **Deauthorize Token Contract:** Revokes authorization for a CW721 contract to send NFTs to the Auction ADO.
    *   **Update Auction:** Modifies the parameters of an auction before it has started.
    *   **Cancel Auction:** Terminates an auction before it has started. 
    *   **Place Bid (CW20):**  Submits a bid using CW20 tokens for the specified NFT in the auction.
    *   **Place Bid:** Submits a bid using native tokens for the specified NFT in the auction.
    *   **Claim:** Distributes the NFT to the winner and the funds to the seller after the auction has concluded.
*   **Query Messages:**
    *   **Latest Auction State:** Retrieves information about the most recent auction for a given NFT, including start/end time, highest bid, auction ID, and other details.
    *   **Auction State:** Fetches the state of a specific auction using its auction ID. 
    *   **Bids:**  Retrieves bids for a given auction, allowing for pagination and sorting.
    *   **Auction IDs:** Returns a list of auction IDs associated with a particular NFT.
    *   **Auction Infos For Address:** Gets information about auctions associated with a specific CW721 contract address, enabling pagination.
    *   **Is Cancelled:** Checks if a specific auction for an NFT has been cancelled.
    *   **Is Closed:**  Verifies if a specific auction for an NFT has been closed (cancelled, claimed, or expired).
    *   **Is Claimed:** Checks if the NFT has been claimed by the winner after the auction ended.
    *   **Authorized Addresses:** Retrieves a list of authorized CW721 contract addresses that can send NFTs to the auction, allowing for pagination and sorting.

### App ADO

*   **Execute Messages:**
    *   **Add App Component:** Includes a new ADO component to the App.
    *   **Claim Ownership:** Sends an ownership request for a component within the App to a specified address. 
    *   **Proxy Message:** Executes a message on behalf of the App for a specific component, requiring app ownership of the component. 
    *   **Update Address:** Modifies the contract address associated with a component within the App. 
*   **Query Messages:**
    *   **Get Address:** Retrieves the contract address of a component using its name. 
    *   **Get Addresses With Names:** Returns a list of all components in the App, including their names and addresses. 
    *   **Get Components:** Fetches a list of all ADO components within the App. 
    *   **Config:** Queries the configuration of the App, including owner and name.
    *   **Component Exists:** Checks if a component with the specified name exists within the App. 

### Crowdfund ADO 

*   **Execute Messages:**
    *   **Mint:** Creates new NFTs to be sold in the crowdfund, specifying token IDs, metadata, and optional ownership.
    *   **Start Sale:** Initiates a crowdfund sale, including setting the start/end time, price per token, minimum tokens to be sold, maximum tokens per wallet, and recipient of funds. 
    *   **Purchase:** Buys NFTs from the crowdfund sale, with an optional limit on the number of tokens to purchase. 
    *   **Purchase By Token ID:** Buys a specific NFT from the crowdfund sale using its token ID. 
    *   **Claim Refund:**  Allows buyers to claim a refund if the minimum sales target is not met.
    *   **End Sale:** Concludes the sale, distributing NFTs and funds if successful, or issuing refunds if unsuccessful. 
    *   **Update Token Contract:** Changes the CW721 contract address associated with the crowdfund. 
*   **Query Messages:**
    *   **State:** Retrieves information about the current or last crowdfund sale, including expiration, price, minimum tokens sold, amount sold, and recipient of funds. 
    *   **Config:** Fetches the configuration of the crowdfund, including the token address and minting permissions after the sale.
    *   **Available Tokens:** Returns a list of token IDs available for purchase in the crowdfund sale.
    *   **Is Token Available:** Checks if a specific NFT is available for purchase in the sale.

### CW20 ADO

*   **Execute Messages:** 
    *   **Mint:** (If minting is enabled) Creates a specified amount of new tokens and adds them to the recipient's balance.
    *   **Transfer:** Moves a specified amount of tokens from the sender to another address.
    *   **Send:** Transfers a specified amount of tokens to a contract and triggers an action on the receiving contract using an attached message.
    *   **Burn:** Permanently destroys a specified amount of tokens from the sender's balance.
    *   **Increase Allowance:** Grants a spender address the ability to use a specified amount of the sender's tokens. 
    *   **Decrease Allowance:** Reduces the amount of tokens a spender address is allowed to use.
    *   **Transfer From:** Transfers a specified amount of tokens from one address (owner) to another (recipient) using the allowance granted to the sender by the owner. 
    *   **Send From:** Sends a specified amount of tokens from one address (owner) to a contract, potentially triggering an action on the receiving contract using an attached message, using the allowance granted to the sender by the owner.
    *   **Burn From:** Permanently destroys a specified amount of tokens from another address (owner) using the allowance granted to the sender by the owner. 
    *   **Update Marketing:** Modifies the marketing information associated with the token, such as project details and description.
    *   **Upload Logo:**  Adds a logo for the token, either by URL or embedded data. 
    *   **Update Minter:** (If minting is enabled) Changes the address authorized to mint new tokens. 
*   **Query Messages:** 
    *   **Balance:** Retrieves the token balance of a specified address.
    *   **Token Info:** Fetches metadata about the token, including name, symbol, decimals, and total supply.
    *   **Minter:** Returns information about the minter, including address and optional cap on total supply. 
    *   **Allowance:**  Retrieves the allowance granted by an owner address to a spender address, including the amount and expiration.
    *   **All Allowances:** Fetches a list of all allowances granted by a specific owner address, allowing for pagination.
    *   **All Spender Allowances:** Returns a list of all allowances granted to a specific spender address, allowing for pagination.
    *   **All Accounts:** Retrieves a list of addresses holding a balance of the token, allowing for pagination.
    *   **Marketing Info:** Fetches marketing details associated with the token, such as project, description, and logo.
    *   **Download Logo:**  Retrieves the embedded logo data if stored on-chain.

### CW20 Exchange ADO

*   **Execute Messages:** 
    *   **Receive:** (Internal) Receives CW20 tokens sent from the specified token contract and executes an action based on the attached hook message.
    *   **Cw20 Hook Message:**
        *   **Start Sale:** Initiates a sale for the sent CW20 tokens, defining the exchange asset, rate, recipient, start time, and duration.
        *   **Purchase:** Buys CW20 tokens from an ongoing sale using the sent CW20 tokens. 
    *   **Cancel Sale:** Terminates an ongoing sale for a specific exchange asset.
    *   **Purchase:** Buys CW20 tokens from an ongoing sale using native tokens. 
*   **Query Messages:**
    *   **Sale:**  Retrieves information about a specific sale for a given exchange asset, including exchange rate, available tokens, and recipient. 
    *   **Token Address:**  Returns the contract address of the CW20 token being sold in the exchange.
    *   **Sale Assets:**  Fetches a list of assets that can be used to purchase the token in ongoing sales, allowing for pagination.

### CW20 Staking ADO 

*   **Execute Messages:** 
    *   **Receive:** (Internal) Receives CW20 tokens and executes an action based on the attached hook message. 
    *   **Cw20 Hook Message:** 
        *   **Stake Tokens:** Stakes the sent tokens for rewards.
        *   **Update Global Index:** Updates the global reward index upon deposit of valid CW20 tokens.
    *   **Add Reward Token:** Includes a new CW20 token as a reward option for stakers. 
    *   **Unstake Tokens:** Unstakes a specified amount or all staked tokens. 
    *   **Claim Rewards:** Claims any pending rewards earned from staking.
    *   **Update Global Indexes:** Updates the global reward index for specified or all reward assets, typically used when new allocated rewards are deposited. 
*   **Query Messages:** 
    *   **Config:**  Retrieves configuration details of the staking contract, including staking token and the number of reward tokens.
    *   **State:** Fetches the current state of the contract, including the total amount of staked tokens. 
    *   **Staker:**  Returns information about a specific staker, including staked amount, balance, and pending rewards.
    *   **Stakers:** Retrieves a list of stakers with their details, allowing for pagination.

### CW721 ADO

*   **Execute Messages:**
    *   **Mint:** Creates a new NFT, defining its token ID, owner, metadata, and optional extensions. 
    *   **Batch Mint:** Mints multiple NFTs at once, specifying details for each token.
    *   **Transfer Agreement:** Sets a transfer agreement for an NFT, allowing a specified buyer to purchase the NFT for a set price. 
    *   **Transfer NFT:** Transfers ownership of an NFT to a specified address, requiring appropriate permissions.
    *   **Send NFT:** Sends an NFT to an external contract, potentially triggering an action on the receiving contract using an attached message, requiring appropriate permissions.
    *   **Burn:** Permanently destroys the data associated with an NFT. 
    *   **Archive:** Makes an NFT immutable, preventing any further modifications, transfers, or burning.
    *   **Approve:** Grants a specific address permission to transfer, burn, or archive the specified NFT.
    *   **Revoke:** Revokes operator privileges for a specific address and NFT.
    *   **Approve All:**  Grants a specific address permission to manage all NFTs owned by the sender.
    *   **Revoke All:**  Revokes operator privileges for a specific address for all NFTs owned by the sender. 
*   **Query Messages:** 
    *   **Minter:** Returns the address authorized to mint new NFTs. 
    *   **Owner Of:** Retrieves the owner of a specified NFT, including approval details.
    *   **Operator:** Checks the approval status of a given operator for all NFTs owned by a specific address. 
    *   **All Operators:**  Returns a list of all operators with approval to manage NFTs owned by a specific address, allowing for pagination.
    *   **Num Tokens:** Fetches the total number of NFTs minted by the contract. 
    *   **NFT Info:** Retrieves information about a specific NFT, including its metadata and extension data. 
    *   **All NFT Info:**  Queries comprehensive information about a specific NFT, including ownership details and NFT data, with an option to include expired approvals.
    *   **Is Archived:** Checks if a specific NFT is archived. 
    *   **Transfer Agreement:** Returns the transfer agreement, if any, associated with a specific NFT.
    *   **Tokens:**  Retrieves a list of token IDs owned by a specific address, allowing for pagination. 
    *   **All Tokens:** Fetches a list of all token IDs minted by the contract, allowing for pagination.
    *   **Contract Info:** Returns the name and symbol of the NFT collection.
    *   **Approval:** Queries the approval status and expiration for a specific spender and NFT.
    *   **Approvals:**  Retrieves a list of all approvals for a specific NFT, with an option to include expired approvals.

### Lockdrop ADO 

*   **Execute Messages:**
    *   **Receive:** (Internal) Receives CW20 tokens to be used as incentives for the lockdrop.
    *   **Increase Incentives:** (Internal, triggered by Receive) Increases the amount of CW20 incentive tokens available in the lockdrop. 
    *   **Deposit Native:** Deposits native tokens into the lockdrop contract.
    *   **Withdraw Native:** Withdraws native tokens from the lockdrop position, adhering to the withdrawal window and limits. 
    *   **Enable Claims:** Allows users to claim their CW20 incentive tokens after the lockdrop period ends.
    *   **Claim Rewards:** Claims the CW20 incentive tokens earned from participating in the lockdrop.
*   **Query Messages:**
    *   **Config:** Retrieves the configuration parameters of the lockdrop contract, including timestamps, deposit/withdrawal windows, incentive token, native denom, and total incentive amount.
    *   **State:** Fetches the current state of the lockdrop contract, including total native tokens locked and claim status.
    *   **User Info:**  Returns information about a specific user's participation in the lockdrop, including deposited amount, earned rewards, and claim status. 
    *   **Withdrawal Percent Allowed:**  Calculates the maximum percentage of deposited funds that can be withdrawn at a given time during the withdrawal window.

### Marketplace ADO

*   **Execute Messages:** 
    *   **Receive NFT:**  (Internal) Receives an NFT from a CW721 contract and initiates a sale based on provided parameters.
    *   **Start Sale:** Begins a sale for the sent NFT, defining the price, accepted denom, start time, duration, and optional recipient of funds. 
    *   **Receive:** (Internal) Handles receiving CW20 tokens to be used for purchasing an NFT. 
    *   **Cw20 Hook Message:**
        *   **Buy:** Purchases an NFT using the sent CW20 tokens.
    *   **Update Sale:**  Modifies the price and accepted denom for the sale of a specific NFT. 
    *   **Buy:** Purchases an NFT using native tokens. 
    *   **Cancel Sale:**  Terminates the sale of a specific NFT. 
*   **Query Messages:** 
    *   **Latest Sale State:** Retrieves information about the most recent sale for a given NFT, including sale ID, price, status, and other details. 
    *   **Sale State:** Fetches the state of a specific sale using its sale ID.
    *   **Sale IDs:** Returns a list of sale IDs associated with a particular NFT. 
    *   **Sale Infos For Address:** Gets information about sales associated with a specific CW721 contract address, enabling pagination. 

### Merkle-Airdrop ADO

*   **Execute Messages:**
    *   **Register Merkle Root:** Sets a Merkle root for a specific airdrop stage, defining the whitelisted addresses eligible to claim tokens.
    *   **Claim:** Allows a whitelisted address to claim their allocated tokens from the airdrop for a specific stage using a Merkle proof.
    *   **Burn:**  Destroys any remaining unclaimed tokens after the expiration of a specific airdrop stage.
*   **Query Messages:**
    *   **Config:** Retrieves the asset type used in the airdrop.
    *   **Merkle Root:** Fetches the Merkle root for a specific airdrop stage, including expiration and total amount.
    *   **Latest Stage:** Returns the ID of the most recent airdrop stage.
    *   **Is Claimed:**  Checks if a specific address has claimed their tokens for a particular airdrop stage.
    *   **Total Claimed:**  Retrieves the total amount of tokens claimed for a specific airdrop stage.

### Rate Limiting Withdrawals ADO 

*   **Execute Messages:**
    *   **Deposit:** Deposits funds to the contract for a specified recipient.
    *   **Withdraw Funds:** Withdraws a specified amount of funds from the contract, adhering to the defined withdrawal limits and frequency.
*   **Query Messages:** 
    *   **Coin Allowance Details:** Returns information about the allowed coin, withdrawal limit, and minimum withdrawal frequency.
    *   **Account Details:** Retrieves the balance and latest withdrawal time for a specified address.

### Splitter ADO 

*   **Execute Messages:**
    *   **Update Recipients:** Modifies the list of recipient addresses and their corresponding percentage allocations, only possible when the contract is not locked. 
    *   **Update Lock:** Locks the contract for a specified period, preventing any modifications to the recipient list and percentages.
    *   **Send:**  Splits any attached funds among the defined recipients according to their allocated percentages. 
*   **Query Messages:**
    *   **Get Splitter Config:**  Retrieves the current configuration of the splitter contract, including the recipient list, percentages, and lock status. 

### Timelock ADO

*   **Execute Messages:**
    *   **Hold Funds:**  Places funds in escrow with an optional condition for release, either based on time expiration or a minimum fund deposit threshold.
    *   **Release Funds:** Releases any held funds for a specified recipient or the sender if no recipient is provided, allowing for pagination.
    *   **Release Specific Funds:** Releases funds held for a specific owner and recipient if the condition is met. 
*   **Query Messages:**
    *   **Get Locked Funds:**  Retrieves information about funds held in escrow for a specific owner and recipient, including the amount, condition, and recipient details. 
    *   **Get Locked Funds For Recipient:** Fetches information about all funds held in escrow for a specified recipient, allowing for pagination.