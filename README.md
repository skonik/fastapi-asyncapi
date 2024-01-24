# fastapi-asyncapi
Use pydantic model to construct asyncapi docs and serve inside your asyncapi app


## Installation

Install using `pip` or `poetry`

```shell 
poetry add fastapi-asyncapi
```

## Usage

1. Describe your specification using pydantic models
```python
from asyncapi_container.asyncapi.spec.v3.info import Info
from asyncapi_container.containers.v3.simple_spec import SimpleSpecV3
from asyncapi_container.custom_types import RoutingMap
from pydantic import BaseModel, Field


class Customer(BaseModel):
    first_name: str = Field(..., title='First Name')
    last_name: str = Field(..., title='Last Name')
    email: str = Field(..., title='Email', format="email", description="Person's email to deliver order info")
    country: str = Field(..., title='Country')
    zipcode: str = Field(..., title='Zipcode')
    city: str = Field(..., title='City')
    street: str = Field(..., title='Street')
    apartment: str = Field(..., title='Apartment')


class OrderSchemaV1(BaseModel):
    product_id: int = Field(..., title='Product Id')
    quantity: int = Field(..., title='Quantity')
    customer: Customer


class MySpecialServiceAsyncAPISpecV3(SimpleSpecV3):
    info: Info = Info(
        title="My special Service",
        version="1.0.0",
        description="Service for making orders"
    )
    sends: RoutingMap = {
        "shop.orders.v1": [
            OrderSchemaV1,
        ]
    }
    receives: RoutingMap = {}
```

2. Configure f`astapi-asyncapi` app using asyncapi spec definition from above Mount `fastapi-asyncapi` urls.

```python

from fastapi import FastAPI

from fastapi_asyncapi.views.sync.v3.asyncapi import fastapi_asyncapi_app

app = FastAPI()

fastapi_asyncapi_app.configure(asyncapi_spec_classes=[MySpecialServiceAsyncAPISpecV3])
app.mount("/docs/", fastapi_asyncapi_app)

```

3. Enjoy your results by navigating to `docs/asyncapi/v3/`

![alt text](docs/img/result.png)