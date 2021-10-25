```
    public static String getMsgTypeRemark(String code) {
        for (MsgTypeEnum value : values()) {
            if (value.code.equals(code)) {
                return value.getRemark();
            }
        }
        return "";
    }
```

