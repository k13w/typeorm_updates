# Typeorm Updates
## Fix Breaking changes update typeorm

##### Update to version 0.2.42

https://github.com/typeorm/typeorm/releases/tag/0.2.42

###### improve DeepPartial type

https://github.com/typeorm/typeorm/commit/b93416d7bc25006b34a90c14c497cc7e6e57e28c

```
0.41.0 below
export type DeepPartial<T> = {
    [P in keyof T]?:
        T[P] extends Array<infer U> ? Array<DeepPartial<U>> :
        T[P] extends ReadonlyArray<infer U> ? ReadonlyArray<DeepPartial<U>> :
        DeepPartial<T[P]> | T[P]
};
```


```
0.42.0 above
export type DeepPartial<T> = T extends object ? {
    [P in keyof T]?: DeepPartial<T[P]>;
} : T;
```


### Breaking changes

```
old TypeOrmBaseDAO.ts
import { DeepPartial } from 'typeorm';
import { BaseRepository } from 'typeorm-transactional-cls-hooked';

export class TypeOrmBaseDAO<TEntity> extends BaseRepository<TEntity> {
  createAndSave(entity?: DeepPartial<TEntity>): Promise<TEntity> {
    return this.save(entity ? this.create(entity) : this.create());
  }
}

```

```
TaypeormBaseDao.ts update
import { BaseRepository } from 'typeorm-transactional-cls-hooked';
import { DeepPartial } from 'typeorm';

export class TypeOrmBaseDAO<TEntity> extends BaseRepository<TEntity> {
  async createAndSave(entity?: DeepPartial<TEntity>): Promise<TEntity> {
    const createdEntity = entity ? this.create(entity) : this.create();
    return await this.manager.save(this.metadata.target, createdEntity);
  }
}
```
