

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

