# Roundies Solana Guides: Update Collection Names and URIs

Something went wrong minting your collection, and now your collection is showing as "unknown"
in your user's wallets! This is a disaster! But don't dive into the bomb shelter yet, it's
easy and cheap to fix this.

The problem boils down to this: Metaplex's latest metadata standard includes a feature called
the "collection standard."

<https://docs.metaplex.com/token-metadata/specification#collections>

This allows a single token on-chain to be responsible for describing
a collection. Each token in the collection has a reference to this collection token, which
contains general metadata for the collection as a whole.

Here's a working on-chain example, a Serum Surfer's collection token: <https://explorer.solana.com/address/EpkE4wSUeyX2PS8HKQofPU4UdNRQUP3bdJJjMeLF18AC/metadata>

Solana wallets for browsers and mobile devices have begun rolling out support for this feature 
and deprecating support for the previous method they used to group NFTs together in a single
collection. That is, inferring the collection from each single NFT's metadata, which is
inefficient from a software perspective.

For me, the `name` and `URI properties of my collections metadata were missing or incorrect.
I still don't know exactly why this happened, as I followed the Candy Machine guides to the
best of my ability. ¯\_(ツ)_/¯

## How to fix it

Basically, you're going to create a token, or a NFT representing your collection. This is
composed of an image for your collection and metadata that describes your collection. As
of the time of this writing, this data is what Solana wallets ike Phantom use to organize
NFTs from a collection.

Here's what you'll do:

1. Create a JSON file with Metaplex Standard NFT metadata inside of it.
2. Create an image to represent your collection.
3. Upload your JSON file and related image to `arweave` using the
   [arloader](https://github.com/CalebEverett/arloader) CLI tool.
4. Wait for arweave to confirm your upload at least 25 times.
5. Use the [metaboss](https://metaboss.rs) CLI tool to update your existing collection NFT's
   on-chain data.

## Prerequisites

- A local keypair with authority to change your existing on-chain collection
- Install [arloader](https://github.com/CalebEverett/arloader)
- Install [metaboss](https://metaboss.rs)

## Create new collection metadata

First you'll need to pull together your metadata.

At the end of the day, the metadata must match Metaplex's [NFT Metadata standard](https://docs.metaplex.com/token-metadata/Versions/v1.0.0/nft-standard)

Put this JSON data in a file called `0.json`.

You can use this format if you like:

```json
{
  "name": "Your Collection Name",
  "symbol": "YOURSYMBOL",
  "description": "Your descriptions.",
  "seller_fee_basis_points": 999,
  "external_url": "https://your-url.tld",
  "image": "0.png",
  "properties": {
    "creators": [
      {
        "address": "CREATOR01ADDRESS",
        "share": 50
      },
      {
        "address": "CREATOR02ADDRESS",
        "share": 50
      },
    ],
    "files": [
      {
        "uri": "0.png",
        "type": "image/png"
      },
    ]
  },
  "collection": {
    "name": "Your Collection Name",
    "family": "Your Collection Family"
  }
}
```

## Create an image for your collection

This image will show as the default item for your collection in wallets. It should be around 100kb or less.

Call it `0.png` and put it in a directory next to your JSON file that you created in the previous step.

This project puts both files in an `assets` folder like this:

```shell
---/assets
--------/0.png
--------/0.json
```

## Locate the path to your local Solana keypair

Typically, the Solana wallet keypair with the "authority" over your collection will have to
be available on your local file system to do this. See [this article](https://docs.solana.com/wallet-guide/file-system-wallet) if you're unsure what that means.

For example, on a Mac computer, a keypair resides at:

`/Users/your_username/.config/solana/id.json`

## Upload your data to arweave

Next we'll use `arloader` to permanently upload our data to the [Arweave](https://www.arweave.org/) network.

At this point I recommend following the instructions from `arloader` directly: <https://github.com/CalebEverett/arloader#upload-assets>

## Wait for arweave to confirm your transaction at least 25 times

This process is described here: <https://github.com/CalebEverett/arloader#confirm-all-transactions>

This took about 30 minutes for me.

## Find your metadata URI

Once you see that arweave has finished propagating your data on its network, find the URI to the JSON
for your collection:

This is described here: <https://github.com/CalebEverett/arloader#get-links-to-uploaded-metadata>

## Update the data on your collection token

Updating the URI: <https://metaboss.rs/update.html#update-uri>

Updating the name: <https://metaboss.rs/update.html#update-name>

Execute these commands:

`metaboss update uri --keypair ~/.config/solana/id.json --account yourcollectiontokenaddresshere --new-uri https://arweave.net/YOURURIHERE`

`metaboss update name --keypair~/.config/solana/id.json --account yourcollectiontokenaddresshere --new-name 'Your Collection Name Here'`

They shouldn't take very long to execute and complete, but it may take a few minutes to start
showing up on indexes or in wallets. It took about 5 minutes for me

Hopefully this was helpful! Feel free to open an issue on this project if you have questions.

The best place to get help is still going to be the Metaplex Discord.

Bye!

## About The Roundies

2222 Roundies living on the Solana blockchain

<https://www.theroundies.co/>

[@TheRoundies](https://twitter.com/TheRoundies)
