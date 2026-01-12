<form th:action="@{/products}" method="get">
    <input type="text" name="keyword" th:value="${keyword}" placeholder="T?m s?n ph?m..."/>

    <select name="type">
        <option value="">-- Ch?n lo?i --</option>
        <option th:each="t : ${types}"
                th:value="${t.id}"
                th:text="${t.name}"
                th:selected="${t.id == type}">
        </option>
    </select>

    <button type="submit">Search</button>
</form>

@GetMapping("/products")
public String searchProducts(@RequestParam(required = false) String keyword, Model model) {
    List<Product> products;
    if (keyword != null && !keyword.isEmpty()) {
        products = productService.search(keyword);
    } else {
        products = productService.findAll();
    }

    model.addAttribute("products", products);
    model.addAttribute("keyword", keyword); // gi? l?i keyword
    return "products"; // tên template
}



Map<Long, Integer> purchasedMap;
query.from(product)
     .orderBy(
         new CaseBuilder()
             .when(product.id.in(purchasedMap.keySet()))
             .then(Expressions.constant(0)) // đ? mua
             .otherwise(Expressions.constant(1)) // chưa mua
             .asc(),
         // ti?p theo sort theo s? lư?ng trong session
         new CaseBuilder()
             .when(product.id.in(purchasedMap.keySet()))
             .then(Expressions.constantMap(purchasedMap)) // mapping spId -> quantity
             .otherwise(Expressions.constant(0))
             .desc()
     )
     .fetch();


    public Page<MstProduct> findProducts(
            Pageable pageable,
            Map<Long, Integer> purchasedMap
    ) {

        QMstProduct product = QMstProduct.mstProduct;

        // Guard null + empty
        Map<Long, Integer> safeMap = purchasedMap != null ? purchasedMap : Collections.emptyMap();
        Set<Long> purchasedIds = safeMap.keySet();
        boolean hasPurchased = !purchasedIds.isEmpty();

        BooleanExpression isPurchased = hasPurchased
                ? product.id.in(purchasedIds)
                : Expressions.FALSE;

        JPAQuery<MstProduct> query = queryFactory
                .selectFrom(product);

        if (hasPurchased) {
            query.orderBy(
                    new CaseBuilder()
                            .when(isPurchased).then(0)
                            .otherwise(1)
                            .asc(),
                    new CaseBuilder()
                            .when(isPurchased)
                            .then(Expressions.constantMap(safeMap))
                            .otherwise(0)
                            .desc()
            );
        } else {
            // fallback sort m?c đ?nh
            query.orderBy(product.createdAt.desc());
        }

        // paging
        List<MstProduct> content = query
                .offset(pageable.getOffset())
                .limit(pageable.getPageSize())
                .fetch();

        long total = queryFactory
                .selectFrom(product)
                .fetchCount();

        return new PageImpl<>(content, pageable, total);
    }

private static final int PRIORITY_PURCHASED = 0;
private static final int PRIORITY_NOT_PURCHASED = 1;
     
     
     
     
fetch("http://localhost:8080/cart/add", {
  method: "POST", // thư?ng dùng POST đ? g?i d? li?u
  headers: {
    "Content-Type": "application/json" // báo server d? li?u d?ng JSON
  },
  body: JSON.stringify({
    id: productId,
    quantity: quantity
  })
})
  .then(response => {
    if (!response.ok) {
      throw new Error("Network response was not ok " + response.statusText);
    }
    return response.json();
  })
  .then(data => {
    console.log("Success:", data);
  })
  .catch(error => {
    console.error("Error:", error);
  });
  
public class CartController {

    @PostMapping("/add")
    public ResponseEntity<String> addToCart(@RequestBody CartItemRequest request) {
        Long productId = request.getId();
        Integer quantity = request.getQuantity();

        // x? l? logic thêm vào gi? hàng
        System.out.println("Product ID: " + productId + ", Quantity: " + quantity);

        return ResponseEntity.ok("Added to cart");
    }
}


<form th:action="@{/cart/add}" method="post">
    <input type="hidden" name="id" th:value="${product.id}" />
    <input type="number" name="quantity" th:value="1" min="1" />
    <button type="submit">Add to Cart</button>
</form>


@PostMapping("/cart/add")
public String addToCart(@ModelAttribute CartItemRequest request, Model model) {

    System.out.println("Product ID: " + request.getId() + ", Quantity: " + request.getQuantity());
    return "cart";
}

