<script>
  import {
    Textarea,
    Row,
    Col,
    Container,
    TextField,
    Button,
    Dialog,
    Snackbar,
  } from "svelte-materialify";

  let secretKey = "";
  let plainText = "";
  let cipherText = "";
  let warningText = "";
  let warningDialogActive = false;
  let encodeInfoActive = false;
  let decodeInfoActive = false;

  const rules = [(v) => (v.length <= 10 && v.length >= 3) || ""];

  // 加密原则是，前三位为编码位，最后一位为分隔符
  const encode = (text, key) => {
    const keyList = key.split("");
    const textList = text.split("");
    const encodeCharList = textList.map((item) => {
      const charIndex = item.charCodeAt(0).toString(keyList.length - 1);
      return charIndex
        .split("")
        .map((char) => keyList[char])
        .join("");
    });
    return encodeCharList.join(keyList[keyList.length - 1]);
  };

  const decode = (text, key) => {
    const keyList = key.split("");
    const encodeCharList = text.split(keyList[keyList.length - 1]);
    const decodeCharList = encodeCharList.map((item) => {
      const charIndexList = item.split("").map((item) => {
        const index = keyList.indexOf(item);
        if (index === -1) {
          throw Error("decode failed, invalid char");
        }
        return index;
      });
      return parseInt(charIndexList.join(""), keyList.length - 1);
    });
    const result = decodeCharList
      .map((item) => String.fromCharCode(item))
      .join("");
    return result;
  };

  const checkSecretKeyValid = () => {
    if (secretKey.length > 10 || secretKey.length < 3) {
      warningText = "密钥的字符数必须在3-10之间。";
      return false;
    }

    const sameCharRegExp = /(.).*\1/i;
    if (sameCharRegExp.test(secretKey)) {
      warningText = "密钥的字符不能重复。";
      return false;
    }

    return true;
  };

  const checkPlainTextValid = () => {
    if (plainText.length === 0) {
      warningText = "请输入明文。";
      return false;
    }

    return true;
  };

  const checkCipherTextValid = () => {
    if (cipherText.length === 0) {
      warningText = "请输入密文。";
      return false;
    }

    if (cipherText.split("").some((char) => secretKey.indexOf(char) === -1)) {
      warningText = "密文中与密钥字符不匹配。";
      return false;
    }

    return true;
  };

  const initInfoAndWarningText = () => {
    encodeInfoActive = false;
    decodeInfoActive = false;
    warningText = "";
  };

  const handleEncodeButtonClick = () => {
    initInfoAndWarningText();
    const validSecretKey = checkSecretKeyValid();
    const validPlainText = checkPlainTextValid();
    if (validSecretKey && validPlainText) {
      cipherText = encode(plainText, secretKey);
      document.getElementById("cipherTextArea").focus();
      encodeInfoActive = true;
    } else {
      warningDialogActive = true;
    }
  };

  const handleDecodeButtonClick = () => {
    initInfoAndWarningText();
    const validSecretKey = checkSecretKeyValid();
    const validCipherText = checkCipherTextValid();
    if (validSecretKey && validCipherText) {
      plainText = decode(cipherText, secretKey);
      document.getElementById("plainTextArea").focus();
      decodeInfoActive = true;
    } else {
      warningDialogActive = true;
    }
  };
</script>

<div class="card">
  <Container>
    <Row>
      <Col>
        <Textarea
          noResize={true}
          class="pa-2"
          outlined
          bind:value={plainText}
          placeholder="明文"
          id="plainTextArea"
        >
          明文
        </Textarea>
      </Col>
      <Col>
        <Textarea
          noResize={true}
          class="pa-2"
          outlined
          bind:value={cipherText}
          placeholder="密文"
          id="cipherTextArea"
        >
          密文
        </Textarea>
      </Col>
    </Row>
    <Row>
      <Col>
        <TextField
          clearable
          outlined
          {rules}
          bind:value={secretKey}
          placeholder="密钥（最少2个字符，最多10个字符）"
        >
          密钥
        </TextField>
      </Col>
    </Row>
    <Row class="align-center">
      <Col sm={4} md={4} lg={4} xl={4} />
      <Col sm={2} md={2} lg={2} xl={2} class="align-self-center">
        <Button
          size="large"
          block
          on:click={handleEncodeButtonClick}
          class="primary-color"
        >
          开始加密
        </Button>
      </Col>
      <Col sm={2} md={2} lg={2} xl={2} class="align-self-center">
        <Button
          size="large"
          block
          on:click={handleDecodeButtonClick}
          class="primary-color"
        >
          开始解密
        </Button>
      </Col>
      <Col sm={4} md={4} lg={4} xl={4} />
    </Row>
  </Container>
  <Dialog class="pa-4 text-center" bind:active={warningDialogActive}>
    <div>
      {warningText}
    </div>
  </Dialog>
  <Snackbar
    style="background-color: #fafafa; color: #212121; margin: 0"
    bind:active={encodeInfoActive}
    top
    center
    timeout={3000}
  >
    加密成功，快去和其他谜语人交流吧。
  </Snackbar>
  <Snackbar
    style="background-color: #fafafa; color: #212121; margin: 0"
    bind:active={decodeInfoActive}
    top
    center
    timeout={3000}
  >
    解密成功，快看看其他谜语人都说了些什么。
  </Snackbar>
</div>

<style>
  .card {
    margin: 30px 20px;
    border-radius: 4px;
    box-shadow: 0 3px 1px -2px rgb(0 0 0 / 20%), 0 2px 2px 0 rgb(0 0 0 / 14%),
      0 1px 5px 0 rgb(0 0 0 / 12%);
  }
</style>
