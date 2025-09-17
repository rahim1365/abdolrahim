from fastapi import APIRouter, HTTPException
from pydantic import BaseModel
from typing import Optional


router = APIRouter()


class Item(BaseModel):
id: Optional[int]
name: str
description: Optional[str] = None


# In-memory store for example (replace with DB)
_DB = {1: {"id": 1, "name": "example", "description": "example item"}}
_NEXT_ID = 2


@router.get("/items", response_model=list[Item])
async def list_items():
return list(_DB.values())


@router.get("/items/{item_id}", response_model=Item)
async def get_item(item_id: int):
item = _DB.get(item_id)
if not item:
raise HTTPException(status_code=404, detail="Item not found")
return item


@router.post("/items", response_model=Item, status_code=201)
async def create_item(item: Item):
global _NEXT_ID
item.id = _NEXT_ID
_DB[_NEXT_ID] = item.dict()
_NEXT_ID += 1
return item


@router.put("/items/{item_id}", response_model=Item)
async def update_item(item_id: int, item: Item):
if item_id not in _DB:
raise HTTPException(status_code=404, detail="Item not found")
item.id = item_id
_DB[item_id] = item.dict()
return item


@router.delete("/items/{item_id}", status_code=204)
async def delete_item(item_id: int):
if item_id not in _DB:
raise HTTPException(status_code=404, detail="Item not found")
del _DB[item_id]
return None
