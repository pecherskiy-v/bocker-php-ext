# Создаем и используем новый билдер
docker buildx create --name mybuilder --bootstrap --use

# Переключаемся на уже созданный билдер
docker buildx use mybuilder

# Получаем список всех билдов
docker buildx ls
