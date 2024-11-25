<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Million Dollar Pixel - Buy with Mencoin</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Million Dollar Pixel - Buy Pixels with Mencoin</h1>
    </header>

    <div id="pixel-grid">
        <!-- Dynamically generate pixels here -->
    </div>

    <footer>
        <p>Powered by Solana & Mencoin</p>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/@solana/web3.js@1.30.0/dist/web3.min.js"></script>
    <script src="script.js"></script>
</body>
</html>
body {
    font-family: Arial, sans-serif;
    text-align: center;
    background-color: #f0f0f0;
    margin: 0;
    padding: 0;
}

header {
    background-color: #333;
    color: white;
    padding: 10px;
}

h1 {
    margin: 0;
}

#pixel-grid {
    display: grid;
    grid-template-columns: repeat(20, 1fr);
    grid-gap: 2px;
    justify-content: center;
    padding: 20px;
}

.pixel {
    width: 30px;
    height: 30px;
    background-color: #ccc;
    cursor: pointer;
    transition: background-color 0.3s;
}

.pixel:hover {
    background-color: #aaa;
}

footer {
    background-color: #333;
    color: white;
    padding: 10px;
    position: absolute;
    width: 100%;
    bottom: 0;
}
document.addEventListener('DOMContentLoaded', function () {
    const gridSize = 20; // 20x20 grid
    const pixelGrid = document.getElementById('pixel-grid');

    // Dynamically create the pixel grid
    for (let i = 0; i < gridSize * gridSize; i++) {
        const pixel = document.createElement('div');
        pixel.classList.add('pixel');
        pixel.setAttribute('data-id', i);
        pixel.addEventListener('click', () => buyPixel(i)); // Add click event to each pixel
        pixelGrid.appendChild(pixel);
    }

    // Connect to Solana wallet
    const connectToWallet = async () => {
        try {
            const { solana } = window;
            if (solana && solana.isPhantom) {
                await solana.connect();
                console.log("Wallet connected:", solana.publicKey.toString());
                return solana;
            } else {
                alert("Please install Phantom wallet to proceed.");
            }
        } catch (error) {
            console.error(error);
            alert("Error connecting to wallet.");
        }
    };

    // Handle the purchase of a pixel using Mencoin on Solana
    const buyPixel = async (pixelId) => {
        const solana = await connectToWallet();

        if (solana) {
            const connection = new solanaWeb3.Connection(solanaWeb3.clusterApiUrl('devnet'), 'confirmed');
            const transaction = new solanaWeb3.Transaction();

            // Define the recipient address and the amount to pay in Mencoin
            const recipientAddress = 'YourSolanaAddressHere';
            const amountInLamports = 1000000000; // Example amount in Lamports (adjust as needed)

            // Create the transfer transaction
            const transferInstruction = solanaWeb3.SystemProgram.transfer({
                fromPubkey: solana.publicKey,
                toPubkey: new solanaWeb3.PublicKey(recipientAddress),
                lamports: amountInLamports,
            });

            transaction.add(transferInstruction);

            // Send the transaction
            try {
                const { signature } = await solana.sendTransaction(transaction, connection);
                await connection.confirmTransaction(signature);
                alert(`Pixel ${pixelId} bought with Mencoin!`);
                // Optionally change the pixel color after purchase
                document.querySelector(`.pixel[data-id='${pixelId}']`).style.backgroundColor = '#FF0000'; // Change color to red
            } catch (err) {
                console.error("Transaction failed:", err);
                alert("Transaction failed, please try again.");
            }
        }
    };
});
