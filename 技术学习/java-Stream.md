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
```