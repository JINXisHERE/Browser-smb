// server.js

const express = require('express');
const SMB2 = require('smb2');

const app = express();
const smbClient = new SMB2();

// Define route to list available shares
app.get('/shares', async (req, res) => {
    try {
        const shares = await smbClient.listShares();
        res.json(shares);
    } catch (error) {
        console.error(error);
        res.status(500).json({ error: 'Internal server error' });
    }
});

// Define route to download a file from a share
app.get('/shares/:shareName/files/:filePath', async (req, res) => {
    const { shareName, filePath } = req.params;
    try {
        const fileStream = await smbClient.createReadStream(`\\\\${shareName}\\${filePath}`);
        fileStream.pipe(res);
    } catch (error) {
        console.error(error);
        res.status(500).json({ error: 'Internal server error' });
    }
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});