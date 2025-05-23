This is the interface for uploading files to the gateway.

```golang
// StoreFile stores files to the gateway
//
// Receive parameter:
//   - url: gateway url
//   - file: stored file
//   - territory: territory name
//   - mnemonic: polkadot account mnemonic
//
// Return parameter:
//   - string: [fid] unique identifier for the file.
//   - error: error message.
//
// Preconditions:
//  1. Account requires purchasing space, refer to [BuySpace] interface.
//  2. Authorize the space usage rights of the account to the gateway account,
//     refer to the [AuthorizeSpace] interface.
//
// Explanation:
//   - Account refers to the account where you configured mnemonic when creating an SDK.
func StoreFile(url, file, territory, mnemonic string) (string, error)
```

Example code:
```golang
package main

import (
	"bytes"
	"context"
	"fmt"
	"io"
	"log"
	"time"

	sdkgo "github.com/CESSProject/cess-go-sdk"
	"github.com/CESSProject/cess-go-sdk/core/process"
	"github.com/CESSProject/cess-go-sdk/utils"
)

// Substrate well-known mnemonic:
//
//	https://github.com/substrate-developer-hub/substrate-developer-hub.github.io/issues/613
//  - cXgaee2N8E77JJv9gdsGAckv1Qsf3hqWYf7NL4q6ZuQzuAUtB
var MY_MNEMONIC = "bottom drive obey lake curtain smoke basket hold race lonely fit walk"

var RPC_ADDRS = []string{
	//testnet
	"wss://testnet-rpc.cess.network/ws/",
}

const PublicGateway = "http://deoss-pub-gateway.cess.network/"
const PublicGatewayAccount = "cXhwBytXqrZLr1qM5NHJhCzEMckSTzNKw17ci2aHft6ETSQm9"
const UploadFile = "Your File"
const Territory = "Your Territory"

func main() {
	sdk, err := sdkgo.New(
		context.Background(),
		sdkgo.ConnectRpcAddrs(RPC_ADDRS),
		sdkgo.Mnemonic(MY_MNEMONIC),
	)
	if err != nil {
		panic(err)
	}
	defer sdk.Close()

	puk, err := utils.ParsingPublickey(PublicGatewayAccount)
	if err != nil {
		panic(err)
	}

	// authorize to public gateway
	_, err = sdk.Authorize(puk)
	if err != nil {
		panic(err)
	}

	// upload file to gateway
	fid, err := process.StoreFile(PublicGateway, UploadFile, Territory, MY_MNEMONIC)
	if err != nil {
		panic(err)
	}

	fmt.Println("fid:", fid)
}
```