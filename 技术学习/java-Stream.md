

# 一.stream

https://cloud.tencent.com/developer/article/1187833

分组

```
  Map<Long, Map<Long, List<OrderSaleEntity>>> map = finishOrderDaily.stream().collect(Collectors.groupingBy(OrderSaleEntity::getStoreId, Collectors.groupingBy(OrderSaleEntity::getCustomerId)));
```

```
Map<String, List<String>> collect = detail.stream().collect(Collectors.groupingBy(WarehouseDetailVO::getSkuCode,
                    Collectors.mapping(WarehouseDetailVO::getWarehouseName, Collectors.toList())));
```

```
        Map<String, List<YeePayIndustryCategoryDTO>> listMap = dto.stream().collect(Collectors.groupingBy(YeePayIndustryCategoryDTO::getPrimaryCategory,
                Collectors.mapping(Function.identity(), Collectors.toList())));
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



```
  List<Map<String, Object>> maps = selectPicDictInfo(companyId);
 
 Map<String, Integer> picNamesMap = maps.stream().filter(m -> Objects.equals(MapUtils.getInteger(m, "s_show_flag"), 1)).
                collect(LinkedHashMap::new, (m, v) -> m.put(MapUtils.getString(v, "b_name"), MapUtils.getInteger(v, "b_code")), HashMap::putAll);
```



```
        List<List<Map<String, Object>>> collect = productInfos.stream().collect(Collectors.groupingBy(e -> e.get("s_parent_id"))).values()
                .stream().map(x -> {
                    Map<String, Object> stringObjectMap = x.get(0);
                    x.stream().map(xx -> {
                        CommonPicInfoDto commonPicInfo = new CommonPicInfoDto();
                        commonPicInfo.setId((Long) xx.get("id"));
                        commonPicInfo.setParentId((Long) xx.get("s_parent_id"));
                        commonPicInfo.setPictureType((Integer) xx.get("b_picture_type"));
                        return commonPicInfo;
                    });
                    return x;
                }).collect(Collectors.toList());
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



```
     Integer totalQuantity = itemEntities.stream().mapToInt(StoreAfterSaleOrderItemEntity::getQuantity).sum();

        BigDecimal totalAmount = itemEntities.stream().map(item -> item.getPrice().multiply(BigDecimal.valueOf(item.getQuantity())))
                .reduce(BigDecimal.ZERO, BigDecimal::add).setScale(2, BigDecimal.ROUND_HALF_UP);
```



# 二.时间操作

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

# 三.@FunctionalInterface

https://www.runoob.com/java/java8-functional-interfaces.html

https://blog.51cto.com/sihai/2425915

# 简介

java 8引入了lambda表达式，lambda表达式实际上表示的就是一个匿名的function。

在java 8之前，如果需要使用到匿名function需要new一个类的实现，但是有了lambda表达式之后，一切都变的非常简介。

我们看一个之前讲线程池的时候的一个例子：

```
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        executorService.submit(new Runnable() {
            @Override
            public void run() {
            log.info("new runnable");
            }
        });
```

executorService.submit需要接收一个Runnable类，上面的例子中我们new了一个Runnable类，并实现了它的run（）方法。

上面的例子如果用lambda表达式来重写，则如下所示：

```
executorService.submit(()->log.info("new runnable"));
```

看起是不是很简单，使用lambda表达式就可以省略匿名类的构造，并且可读性更强。

那么是不是所有的匿名类都可以用lambda表达式来重构呢？也不是。

我们看下Runnable类有什么特点：

```java
@FunctionalInterface
public interface Runnable 
```

Runnable类上面有一个@FunctionalInterface注解。