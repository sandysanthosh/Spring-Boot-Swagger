# Spring-Boot-Swagger

Spring Boot CRUD application using Spring Boot and Swagger for documentation


#### Pom.xml:

```

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>


```

#### product.java:

```

@Entity
@Table(name = "products")
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @NotBlank
    private String name;

    @NotBlank
    private String description;

    private Double price;

    // Getters and setters
}


```

#### product Respoitory:

```

public interface ProductRepository extends JpaRepository<Product, Long> {
}

```

#### product Service:

```

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<Product> findAll() {
        return productRepository.findAll();
    }

    public Product findById(Long id) {
        return productRepository.findById(id).orElse(null);
    }

    public Product save(Product product) {
        return productRepository.save(product);
    }

    public void deleteById(Long id) {
        productRepository.deleteById(id);
    }
}


```

#### Rest Controller:

```

@RestController
@RequestMapping("/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping
    public List<Product> findAll() {
        return productService.findAll();
    }

    @GetMapping("/{id}")
    public Product findById(@PathVariable Long id) {
        return productService.findById(id);
    }

    @PostMapping
    public Product save(@RequestBody Product product) {
        return productService.save(product);
    }

    @DeleteMapping("/{id}")
    public void deleteById(@PathVariable Long id) {
        productService.deleteById(id);
    }
}

```

#### Enable Swagger 2:

```

@Configuration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket api() {
        return


```

#### Junit test case:


```

@RunWith(SpringRunner.class)
@WebMvcTest(ProductController.class)
public class ProductControllerTest {
   
   @Autowired
private MockMvc mockMvc;

@MockBean
private ProductService productService;



@Test
public void testFindAllProducts() throws Exception {
    // Arrange
    List<Product> products = Arrays.asList(
            new Product(1L, "Product 1", "Description 1", 10.99),
            new Product(2L, "Product 2", "Description 2", 20.99)
    );
    given(productService.findAll()).willReturn(products);

    // Act and Assert
    mockMvc.perform(get("/products"))
            .andExpect(status().isOk())
            .andExpect(content().json("[{'id': 1,'name': 'Product 1','description': 'Description 1','price': 10.99}, {'id': 2,'name': 'Product 2','description': 'Description 2','price': 20.99}]"));
}

@Test
public void testFindProductById() throws Exception {
    // Arrange
    Long id = 1L;
    Product product = new Product(id, "Product 1", "Description 1", 10.99);
    given(productService.findById(id)).willReturn(product);

    // Act and Assert
    mockMvc.perform(get("/products/1"))
            .andExpect(status().isOk())
            .andExpect(content().json("{'id': 1,'name': 'Product 1','description': 'Description 1','price': 10.99}"));
}

 mockMvc.perform(get("/products"))
                .andDo(MockMvcResultHandlers.print())
                .andExpect(status().isOk())
                .andExpect(content().json("[{'id': 1,'name': 'Product 1','description': 'Description 1','price': 10.99}, {'id': 2,'name': 'Product 2','description': 'Description 2','price': 20.99}]"));
                
}


```


Note: The above test methods use the MockMvc class to simulate HTTP requests and the MockBean annotation to mock the service class. Also, the test methods use the given() method from the BDDMockito class to set up the expected behavior of the mock service.

