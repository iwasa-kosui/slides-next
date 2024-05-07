
---
layout: two-cols-header
---

# 業務システムの複雑性

業務システムの開発は _**複雑なビジネスロジック**_ との闘い

- 複雑なドメイン知識  
  例) 会計、請求、医療、建築、物流など
- 法令等への対応  
  例) 医療情報システムガイドラインなど
- 数多くのエンティティ  
  例) 薬剤師、患者、処方箋、薬歴など

---

# fp-ts

```typescript
import { pipe, flow } from 'fp-ts/function';
import { fold, none, Option, some } from 'fp-ts/Option';
import * as Array from 'fp-ts/Array';

type Either<A, B> = Left<A> | Right<B>
```

---