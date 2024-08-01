# Создаем и используем новый билдер
```shell
docker buildx create --name mybuilder --bootstrap --use
```

# Переключаемся на уже созданный билдер
```shell
docker buildx use mybuilder
```

# Получаем список всех билдов
```shell
docker buildx ls
```
