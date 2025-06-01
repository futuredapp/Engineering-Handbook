We use TypeScript across our stack—including NestJS (backend), Vue.js (frontend), and GraphQL/REST APIs—to improve code quality, maintainability, and developer experience. TypeScript’s static typing helps catch errors early, enables better tooling, and makes codebases easier to scale and refactor.

### Why Use TypeScript?

- **Early Error Detection**  
  TypeScript catches type-related errors in your IDE, reducing bugs that would otherwise only surface during testing or runtime.
- **Better Documentation**  
  Types act as self-documenting code, making it easier to understand what functions expect and return.
- **Improved Collaboration**  
  Consistent types and interfaces help team members work together more efficiently, especially in large projects.

### Why Use Style Guides?

- **Consistency**  
  Style guides ensure everyone follows the same conventions, making the codebase easier to read and maintain.
- **Fewer Style Debates**  
  With clear guidelines, less time is wasted discussing formatting or naming during code reviews.
- **Focus on Logic**  
  Automated tools (like ESLint and Prettier) enforce most style rules, so discussions can focus on architecture and logic.

We don’t enforce overly strict rules, but we emphasize modern best practices and consistency.
Here are some basic rules and practices we follow in our codebases, but we encourage flexibility and new ideas as long as they align with our principles.

---

### Example Rules and Practices

#### 1. **Naming Conventions**

- **Variables and functions:** camelCase  

```
const userCount = 10
function getUserById(id: string) { … }
```

- **Classes and interfaces:** PascalCase

```
class UserService { … }
interface UserData { … }
```

- **Private members:** Prefix with underscore (optional, but consistent if used)

```
private _internalState: string;
```

#### 2. **Type Safety and Immutability**

- **Use `readonly` for immutable properties**

```
class User {
    readonly id: string
    constructor(id: string) { 
        this.id = id 
    }
}
```

- **Avoid `any`; prefer explicit types**

```
// Bad 
function logValue(value: any) { … }
// Good
function logValue(value: string | number) { … }
```

#### 3. **GraphQL with NestJS and TypeScript**

- **Define GraphQL types as TypeScript classes**

```
@ObjectType()
class User {
    @Field()
    id: string
    
    @Field()
    name: string
}
```

- **Use auto-generated types for queries and mutations**

```
@Query(() => User)
async getUsers(): Promise<User[]> { … }
```

#### 4. **Vue.js with TypeScript**

- **Use `<script lang="ts">` in Single File Components**
- **Define props with types**

```
props: {
    userId: { type: String as PropType, required: true }
}
```

