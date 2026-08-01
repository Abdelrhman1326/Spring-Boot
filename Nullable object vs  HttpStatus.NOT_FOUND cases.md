``` Java
@Transactional  
public void updateSortOrder(User user, Map<Long, Integer> id_order) {  
    for (Map.Entry<Long, Integer> entity : id_order.entrySet()) {  
        Optional<Link> link = linkRepository.findByIdAndPageUserId(entity.getKey(), user.getId());  
        link.ifPresent(value -> value.setSortOrder(entity.getValue()));  
    }  
}
```

``` Java

```