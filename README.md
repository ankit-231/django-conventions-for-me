# django-conventions-for-me

These are all the Djagno conventions I've learned from different resources and my own experience.
I don't know if they are the best, but I have learned that these are best for me. Feel free to use it in your next project. If you have any suggestions, you can create an issue or a pull request.
These should be okay for small to medium sized projects. These are primarily for my own reference.

Some resources:

- https://github.com/HackSoftware/Django-Styleguide
- https://github.com/feldroy/two-scoops-of-django-3.x (the book)
- various online resources and my experience from companies and open source projects

## Views and Service functions

Views should be extremely thin.
Some of the styles I've used before:

- All business logic in views (not practical in big projects)
- Serializer validation in views, send validated data to a service function. (practical but a caveat is that the full validation logic is still dependent on serializer which is used in views)
- Views call service functions with raw data (for create) and raw data+object (for update). The service function uses serializer's validations and its own validation logics. (from my experience, this is the most practical.

Note: I will provide examples later on.

## Exceptions

Predefine your Exceptions, some of the ones I use are listed below.

- ApplicationError (when raised from anywhere, converted to 400 response). This is useful when you want to immediately send a bad response to client. Usually we return `Response(status=400)` from a APIView to provide the client a 400 error. But with ApplicationError, you can simply raise it from anywhere.
- InternalApplicationError. When raised, converted to 500 response, which has descriptive message when DEBUG is True, but has little to no context when DEBUG is False. When I write a function that is only used by another function (basically the function is called by system itself from another function), I might send some invalid params or values or try to do illegal things with the function. This is to remind (and prevent) me from using the function in the wrong way.

## Error Schema

- This should be consistent. I learned this purely from https://github.com/HackSoftware/Django-Styleguide#errors--exception-handling
- It is managed by creating a custom exception handler. Yes, such thing exists in Django Rest Framework (DRF), it seems daunting at first, but it's really easy once you get the hang of it, and you'll probably only have to configure it once, with occasional updates if required.
  Basically, this should be the expected schema for ANY error that occurs in the system:

```json
{
    "message": "Something failed | Validation error | Permission denied",
    "extra": "it can be anything. A string, a list, a dictionary of errors in detail. Usually serializer's validation error goes to this key."
}
```

## Success Schema

- It should be consistent.
- Even for "204 No Content", it should be consistent.
  Example:

```json
{
    "data": "it can be anything. A string, a list, a null value, a dictionary of data. Usually serializer's data goes to this key."
}
```

Note: even if you have pagination, send properties like below inside the `data` key:

```python
{"data": {"count": 32,
        "results_count": 20,
        "next": "http://localhost:8001/api/v1/products/get/public/list/paginated/?category=accessories&page=2",
        "previous": null,
        "total_pages": 2},
"message": "Success"}
```
