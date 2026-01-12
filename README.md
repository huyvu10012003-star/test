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
             .then(Expressions.constant(0)) // ð? mua
             .otherwise(Expressions.constant(1)) // chýa mua
             .asc(),
         // ti?p theo sort theo s? lý?ng trong session
         new CaseBuilder()
             .when(product.id.in(purchasedMap.keySet()))
             .then(Expressions.constantMap(purchasedMap)) // mapping spId -> quantity
             .otherwise(Expressions.constant(0))
             .desc()
     )
     .fetch();
