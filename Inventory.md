#
# Creating a table
#

aws dynamodb create-table \
    --table-name Inventory \
    --attribute-definitions \
        AttributeName=Code,AttributeType=S \
        AttributeName=Description,AttributeType=S \
    --key-schema \
        AttributeName=Code,KeyType=HASH \
        AttributeName=Description,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5

#
# Inserting an item
#

aws dynamodb put-item \
    --table-name Inventory \
    --item file://inventory.json \

#
# Inserting multiple items
#

aws dynamodb batch-write-item \
    --request-items file://batchInventory.json

#
# Creating a global secundary index based on an item type
#

aws dynamodb update-table \
    --table-name Inventory \
    --attribute-definitions AttributeName=itemType,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"itemType-index\",\"KeySchema\":[{\"AttributeName\":\"itemType\",\"KeyType\":\"HASH\"} ], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

#
# Creating a global secundary index based on item origin
#

aws dynamodb update-table \
    --table-name Inventory \
    --attribute-definitions\
        AttributeName=itemOrigin,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"itemOrigin-index\",\"KeySchema\":[{\"AttributeName\":\"item\",\"KeyType\":\"HASH\"} ], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

#
# Creating a global secundary index based on item description
#

aws dynamodb update-table \
    --table-name Inventory \
    --attribute-definitions\
        AttributeName=Description,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Description-index\",\"KeySchema\":[{\"AttributeName\":\"Description\",\"KeyType\":\"HASH\"} ], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"

#
# Searching an item by Code
#

aws dynamodb query \
    --table-name Inventory \
    --key-condition-expression "Code = :code" \
    --expression-attribute-values  '{":code":{"S":"123"}}'

#
# Searching an item by Description
#

aws dynamodb query \
    --table-name Inventory \
    --index-name DescriptionItem-index \
    --key-condition-expression "DescriptionItem = :description" \
    --expression-attribute-values  '{":description":{"S":"Computer Screen 28 inches"}}'
