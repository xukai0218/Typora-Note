		依赖
		

# 依赖

```
        <dependency>
            <groupId>com.querydsl</groupId>
            <artifactId>querydsl-apt</artifactId>
        </dependency> 
 
 
 
 <!--添加QueryDSL插件支持-->
            <plugin>
                <groupId>com.mysema.maven</groupId>
                <artifactId>apt-maven-plugin</artifactId>
                <version>1.1.3</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>process</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>target/generated-sources/java</outputDirectory>
                            <processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```

# 返回自定义 实体类

```


https://cloud.tencent.com/developer/article/1152904
```



```
 public ServerResponse<PageImpl<BaseRuiguSupplierVO>> queryRuiGuSupplers(QuerySupplierListReq req) {
        QBaseRuiguSupplierEntity supplierEntity = QBaseRuiguSupplierEntity.baseRuiguSupplierEntity;
        // 拼接查询条件
        BooleanBuilder condition = new BooleanBuilder();
        if (req.getSupplierType() != null) {
            condition.and(supplierEntity.supplierType.eq(req.getSupplierType()));
        }
        if (StringUtils.isNotEmpty(req.getKeyWord())) {
            condition.and(supplierEntity.phone.like("%" + req.getKeyWord() + "%"));
            condition.or(supplierEntity.supplierName.like("%" + req.getKeyWord() + "%"));
            condition.or(supplierEntity.contact.like("%" + req.getKeyWord() + "%"));
        }
        condition.and(supplierEntity.deleted.eq(YesOrNoEnum.NO.getCode()));
        Pageable pageable = PageRequest.of(req.getPage(), req.getSize());
        JPAQuery<BaseRuiguSupplierVO> allSupplierEntity = jpaQueryFactory.select(Projections.bean(BaseRuiguSupplierVO.class,
                supplierEntity.id,
                supplierEntity.phone,
                supplierEntity.supplierName,
                supplierEntity.supplierType,
                supplierEntity.contact))
                .from(supplierEntity).where(condition)
                .orderBy(supplierEntity.lastUpdatedAt.desc())
                .fetchAll();
        List<BaseRuiguSupplierVO> voList = allSupplierEntity.offset(pageable.getOffset()).limit(pageable.getPageSize()).fetch();
        return ServerResponse.ok(PageImpl.of(voList, pageable, allSupplierEntity.fetchCount()));
    }
```

