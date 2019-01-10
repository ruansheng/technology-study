### OTP，One-Time Password
```
动态口令（OTP，One-Time Password）又称一次性密码，是使用密码技术实现的在客户端和服务器之间通过共享秘密的一种认证技术，
是一种强认证技术，是增强目前静态口令认证的一种非常方便技术手段，是一种重要的双因素认证技术，动态口令认证技术包括客户端用于生成口令产生器的，
动态令牌，是一个硬件设备，和用于管理令牌及口令认证的后台动态口令认证系统组成。
```

### OTP算法
```
国际上动态口令OTP有2大主流算法:
一个是RSA SecurID : RSA SecurID使用AES对称算法
一个是OATH组织的OTP算法 : OATH使用HMAC算法

如果在国内来说的话，是国密的OTP密码算法: 使用的国密SM1（对称）和SM3（HASH）算法
```

### OTP认证技术的不足
```
动态口令认证技术没有第3方权威机构认证，如果业务应用系统安全策略不完善的情况下，
可能会受到中间人攻击。如某某银行使用时间型动态令牌受到网络钓鱼攻击。
建议在应用中完善安全使用策略，划清口令使用权限，加强交易系统流程控制，用以提高系统安全性。
```

### 项目接入OTP
```
1. 下载Authenticator，这个app内置OTP算法，基于时间同步的令牌算法

2. 构造Authenticator协议
   协议格式为: otpauth://totp/demo.com?secret=cdsfdsesdfresgfs&period=60&digits=8&issuer=Example
      参数解释:
           demo.com 可以替换成其它字符串，这个字符串会出现在Authenticator每个item左下方，可以认为是名称
           secret 指定生成口令的种子密钥，长度为128字节，对应长度为16的字符串
           period=60 参数可以省略，默认值period=30
           digits=8  参数可以省略，默认值digits=6 注意: digits 只能是6~8之间，指定生成的口令长度
           issuer=Example 参数可以省略 显示在Authenticator item 的左上角
   构造好协议，可以在Authenticator手动输入，也可以把这个协议字符串写入二维码，用Authenticator扫描二维码，如果协议错误，会提示无效的协议格式

3. 服务端接入OTP算法
   注意: 如果Authenticator协议上配置的是60秒、或者8位长度的口令，那算法上面要注意达成一致
   后面给出golang的服务端生成动态口令的算法程序
   同一时间服务端生成的code和Authenticator生成的code是一致的
```
[google官方协议wiki](https://github.com/google/google-authenticator/wiki/Key-Uri-Format)

### golang口令算法(也有其他各种语言的算法实现)
```
package returncode

import (
	"crypto/hmac"
	"crypto/sha1"
	"encoding/base32"
	"fmt"
	"os"
	"strings"
	"time"
)

func toBytes(value int64) []byte {
	var result []byte
	mask := int64(0xFF)
	shifts := [8]uint16{56, 48, 40, 32, 24, 16, 8, 0}
	for _, shift := range shifts {
		result = append(result, byte((value>>shift)&mask))
	}
	return result
}

func toUint32(bytes []byte) uint32 {
	return (uint32(bytes[0]) << 24) + (uint32(bytes[1]) << 16) +
		(uint32(bytes[2]) << 8) + uint32(bytes[3])
}

func oneTimePassword(key []byte, value []byte) uint32 {
	// sign the value using HMAC-SHA1
	hmacSha1 := hmac.New(sha1.New, key)
	hmacSha1.Write(value)
	hash := hmacSha1.Sum(nil)

	// We're going to use a subset of the generated hash.
	// Using the last nibble (half-byte) to choose the index to start from.
	// This number is always appropriate as it's maximum decimal 15, the hash will
	// have the maximum index 19 (20 bytes of SHA1) and we need 4 bytes.
	offset := hash[len(hash)-1] & 0x0F

	// get a 32-bit (4-byte) chunk from the hash starting at offset
	hashParts := hash[offset : offset+4]

	// ignore the most significant bit as per RFC 4226
	hashParts[0] = hashParts[0] & 0x7F

	number := toUint32(hashParts)

	// size to 6 digits
	// one million is the first number with 7 digits so the remainder
	// of the division will always return < 7 digits
	pwd := number % 1000000

	return pwd
}

// all []byte in this program are treated as Big Endian
//func main() {
func ReturnCode(secret string) uint32 {
	// decode the key from the first argument
	inputNoSpaces := strings.Replace(secret, " ", "", -1)
	inputNoSpacesUpper := strings.ToUpper(inputNoSpaces)
	key, err := base32.StdEncoding.DecodeString(inputNoSpacesUpper)
	if err != nil {
		fmt.Fprintln(os.Stderr, err.Error())
		os.Exit(1)
	}

	// generate a one-time password using the time at 30-second intervals
	epochSeconds := time.Now().Unix()
	code := oneTimePassword(key, toBytes(epochSeconds/30))

	// 此动态码剩余可用的秒数
	//secondsRemaining := 30 - (epochSeconds % 30)
	//fmt.Printf("%06d-%02d\n", code, secondsRemaining)
	return code
}
```

### 调用示例
```
func main() {
    // otpauth://totp/root.com?secret=cdsfdsesdfresgfs
    randkey := "cdsfdsesdfresgfs"

	code := returncode.ReturnCode(randkey)

	fmt.Println(code)
}
```