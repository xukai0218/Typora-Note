

https://cloud.tencent.com/developer/article/1187833

分组

```
  Map<Long, Map<Long, List<OrderSaleEntity>>> map = finishOrderDaily.stream().collect(Collectors.groupingBy(OrderSaleEntity::getStoreId, Collectors.groupingBy(OrderSaleEntity::getCustomerId)));
```



分类树

```
  private List<BaseCategoryNodeVO> getCategoryNodesTree(List<BaseCategoryEntity> entities) {
        List<BaseCategoryNodeVO> voList = Lists.newArrayList();
        entities.stream().forEach(x -> {
            BaseCategoryNodeVO baseCategoryNodeVO = new BaseCategoryNodeVO();
            BeanUtils.copyProperties(x, baseCategoryNodeVO);
            voList.add(baseCategoryNodeVO);
        });
        List<BaseCategoryNodeVO> categoryTree = Lists.newArrayList();
        voList.stream().filter(parents -> parents.getParentId() == YesOrNoEnum.NO.getCode()).forEach(parent -> {
            parent.setChildren(voList.stream().filter(y -> parent.getId().equals(y.getParentId())).collect(Collectors.toCollection(ArrayList::new)));
            categoryTree.add(parent);
        });
        return categoryTree;
    }
```

// 获取id List

```
List<Long> categoryIds = req.getPromotionProducts().stream().map(CreatePromotionReq.PromotionProduct::getCategoryId).collect(Collectors.toList());
```

to map

```
 Map<String, BaseScenarioVO> voMap = data.stream().collect(Collectors.toMap(BaseScenarioVO::getImage, BaseScenarioVO::get));
 
     public BaseScenarioVO get() {
        return this;
    }
  Map<String, BaseScenarioVO> voMap = data.stream().collect(Collectors.toMap(BaseScenarioVO::getImage, BaseScenarioVO ->BaseScenarioVO));
  toMap(StoreUserEntity::getUserId, Function.identity()))
```

去重

```
    /**
     * 去重
     *
     * @param list fb数据
     * @return 不重复的流水号
     */
    private List<String> distinctFb(List<ClearingStatisticsDetailDTO> list) {
        return list.stream()
                .collect(Collectors.toMap(e -> e.getTransactionSerialNumber(), e -> 1, (a, b) -> a + b))
                .entrySet().stream()
                .filter(entry -> entry.getValue() == 1)
                .map(entry -> entry.getKey())
                .collect(Collectors.toList());
    }
    
             // 排出 重复的订单 并收集
            details.removeIf(order -> {
                if (!unSeriaNums.contains(order.getTransactionSerialNumber())) {
                    fbRepetitionOrder.add(order);
                    return true;
                }
                return false;
            });   
```

Integer -> String

```
List<String> stringList = intList.stream().map(String::valueOf).collect(Collectors.toList());
```



```
Map<String, List<String>> collect = detail.stream().collect(Collectors.groupingBy(WarehouseDetailVO::getSkuCode,
                    Collectors.mapping(WarehouseDetailVO::getWarehouseName, Collectors.toList())));
```





```
shopInfoManager.findTop3ProductImages(shopId).stream().filter(x -> !x.equals(Strings.EMPTY)).collect(Collectors.toList());
```



拼接字符串

```
        String collect = brandIds.stream().map(Object::toString).collect(Collectors.joining(","));

```



list->list

```
            List<BrandProfitInfoVO> infoVOS = brandProfitInfos.stream().map(priceProfit -> {
                BrandProfitInfoVO brandProfitInfo = new BrandProfitInfoVO();
                brandProfitInfo.setId(priceProfit.getId());
                brandProfitInfo.setBrandId(priceProfit.getBrandId());
                brandProfitInfo.setBrandName(brandInfoMap.get(priceProfit.getBrandId()));
                brandProfitInfo.setTargetProfit(priceProfit.getTargetProfit());
                brandProfitInfo.setPerformProfit(priceProfit.getPerformProfit());
                String performProfitTime = brandPerformProfitTimeMap.get(priceProfit.getBrandId());
                if (StringUtils.isNotBlank(performProfitTime)) {
                    ConditionTimeRuleDTO conditionTimeRule = JsonUtil.parseObject(performProfitTime, ConditionTimeRuleDTO.class);
                    brandProfitInfo.setPerformProfitStartTime(conditionTimeRule.getStartTime());
                    brandProfitInfo.setPerformProfitEndTime(conditionTimeRule.getEndTime());
                }
                return brandProfitInfo;
            }).collect(Collectors.toList());

```



时间操作

```
    /**
     * 减一天
     *
     * @param date
     * @return "yyyy-MM-dd"
     * @throws ParseException
     */
    private String getMinusOneDateToStr(String date) throws ParseException {
        return new SimpleDateFormat("yyyy-MM-dd").format(getMinusOneDate(date));
    }

    /**
     * 减一天
     *
     * @param date
     * @return
     * @throws ParseException
     */
    private Date getMinusOneDate(String date) throws ParseException {
        Calendar calendar = Calendar.getInstance();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        calendar.setTime(simpleDateFormat.parse(date));
        calendar.add(Calendar.DAY_OF_MONTH, -1);
        return calendar.getTime();
    }
    
        /**
     * 加一天
     *
     * @param date
     * @return
     * @throws ParseException
     */
    private Date getAddOneDate(String date) throws ParseException {
        Calendar calendar = Calendar.getInstance();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        calendar.setTime(simpleDateFormat.parse(date));
        calendar.add(Calendar.DAY_OF_MONTH, 1);
        return calendar.getTime();
    }
    
    
        /**
     * 拼接 日期 + 时间
     *
     * @param date 2020-08-28
     * @param time " 23:00:00"
     * @return 2020-08-28 23:00:00
     */
    private String getJoinDateAndTime(String date, String time) {
        DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        LocalDateTime ldt = LocalDateTime.parse(date + time, df);
        return df.format(ldt);
    }
    
        /**
     * 日期相减
     *
     * @param clearingDate
     * @param startDate
     * @return
     */
    private long getSubtractDay(String clearingDate, String startDate) {
        DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        LocalDate clearing = LocalDate.parse(clearingDate, df);
        LocalDate start = LocalDate.parse(startDate, df);
        return clearing.toEpochDay() - start.toEpochDay();
    }
```

