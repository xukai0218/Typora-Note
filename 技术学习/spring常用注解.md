```
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
@JsonSerialize(using = LocalDateTimeSerializer.class)
@JsonDeserialize(using = LocalDateTimeDeserializer.class)
@ApiModelProperty(name = "createdAt", value = "创建时间")
```

```

```

