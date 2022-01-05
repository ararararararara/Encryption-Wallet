# Encryption,Wallet

## 암호화의 목적
### 1. 기밀성유지
### 2. 정보의 무결성
### 3. 가용성 (누구나 접근가능)

## 지갑
### 비밀키, 공개키

encryption.js 생성

```
const fs = require("fs")
const ecdsa = require("elliptic") // 타원 곡선 디지털 서명 알고리즘...영지식 증명
const ec = new ecdsa.ec("secp256k1")

const privateKeyLocation = "wallet/" + (process.env.PRIVATE_KEY || "default")
const privateKeyFile = privateKeyLocation + "/private_key"

function initWallet(){
    if (fs.existsSync(privateKeyFile)){
        console.log("기존 지갑 private key 경로: " + privateKeyFile)
        return;
    }
    if (!fs.existsSync("wallet/")){
        fs.mkdir("wallet/")
    }
    if(!fs.existsSync(privateKeyLocation)){
        fs.mkdirSync(privateKeyLocation)
    }
    

    const newPrivateKey = generatePrivateKey();
    fs.writeFileSync(privateKeyFile, newPrivateKey);
    console.log("새로운 지갑 생성 private key 경로 : " + privateKeyFile);
}
function generatePrivateKey(){
    const keyPair = ec.genKeyPair();
    const privateKey = keyPair.getPrivate();
    return privateKey.toString(16);
}

function getPrivatekeyFromWallet(){
    const buffer = fs.readFileSync(privateKeyFile, "utf8");
    return buffer.toString(); //utf8변환해서 썻던 명령어대로 변환해서 내보냄
}   

function getPublickeyFromWallet() {
    const privateKey = getPrivatekeyFromWallet();
    const key = ec.keyFromPrivate(privateKey, "hex");
    return key.getPublic().encode("hex");
}

```
httpServer.js에 추가
```
app.get("/address", (req,res)=>{
		const address = getPublickeyFromWallet().toString();
		if (address !=""){
			res.send({"address" : address});
		}
		else {
			res.send("empty address!");
		}
	})

```
localhost:3001/address에 들어가서 공개키 확인
