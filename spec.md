## [[GetPrototypeOf]] ( )

When the [[GetPrototypeOf]] internal method of a Proxy exotic object _O_ is called, the following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"getPrototypeOf"`).
1. If _trap_ is *undefined*, then
   1. Let _proto_ be ? _original_.[[GetPrototypeOf]]().
1. Else
   1. Let _proto_ be ? Call(_trap_, _handler_, &laquo; _original_ &raquo;).
   1. If Type(_proto_) is neither Object nor Null, throw a *TypeError* exception.

1. Let _extensible_ be ? _O_.[[CommittedIsExtensible]].
1. If _extensible_ is *true*, then
   1. Set _O_.[[CommittedGetPrototype]] to _proto_
   1. Return _proto_.

1. Let _lastProto_ be ? _O_.[[CommittedGetPrototype]].
1. If _lastProto_ is not *undefined*, then
   1. If SameValue(_proto_, lastProto_) is *false*, throw a *TypeError* exception.

1. Set _O_.[[CommittedGetPrototype]] to _proto_
1. Return _proto_.


[[GetPrototypeOf]] for proxy objects enforces the following invariants:

- The result of [[GetPrototypeOf]] must be either an Object or null.
- If proxy is non-extensible, and [[GetPrototypeOf]] returns a value v, then any future calls to [[GetPrototypeOf]] must return the SameValue as v.

## [[SetPrototypeOf]] ( _V_ )

When the [[SetPrototypeOf]] internal method of a Proxy exotic object _O_ is called with argument _V_, the following steps are taken:

1. Assert: Either Type(_V_) is Object or Type(_V_) is Null.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"setPrototypeOf"`).
1. If _booleanResult_ is *undefined*, then
   1. Let _result_ be ? _original_.[[SetPrototypeOf]](_V_).
1. Else,
   1. Let _trap_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_, _V_ &raquo;)).
1. If _booleanResult_ is *false*, return *false*.

1. Let _extensible_ be ? _O_.[[CommittedIsExtensible]].
1. If _extensible_ is *true*, return *true*.

1. Let _lastProto_ be ? _O_.[[CommittedGetPrototype]].
1. If _lastProto_ is not *undefined*, then
   1. If SameValue(_V_, _lastProto_) is *false*, throw a *TypeError* exception.

1. Return *true*.

[[SetPrototypeOf]] for proxy objects enforces the following invariants:

- The result of [[SetPrototypeOf]] is a Boolean value.
- If proxy is non-extensible, [[SetPrototypeOf]] must return false, unless V is the SameValue as the proxy's observed [[GetPrototypeOf]] value.

## [[IsExtensible]] ( )

When the [[IsExtensible]] internal method of a Proxy exotic object _O_ is called, the following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"isExtensible"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ be ? _original_.[[IsExtensible]]().
1. Else,
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_ &raquo;)).

1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
1. If _extensible_ is *false*
   1. If _booleanResult_ is *true*, throw a *TypeError* exception.
1. Else, if _booleanResult_ is *false*
   1. Let _originalIsExtensible_ be ? _original_.[[IsExtensible]]().
   1. If _originalIsExtensible_ is *true*, throw a *TypeError* exception.
   1. Else, set _O_.[[CommittedIsExtensible]] to *false*
1. Return _booleanResult_.

[[IsExtensible]] for proxy objects enforces the following invariants:

- The result of [[IsExtensible]] is a Boolean value.
- If [[IsExtensible]] returns false, all future calls to [[IsExtensible]] on the proxy must return false.
- If [[IsExtensible]] returns false, [[IsExtensible]] applied to the proxy object's original object must return false.

## [[PreventExtensions]] ( )

When the [[PreventExtensions]] internal method of a Proxy exotic object _O_ is called, the following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"preventExtensions"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ be ? _original_.[[PreventExtensions]]().
1. Else,
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_ &raquo;)).

1. If _booleanResult_ is *true*, then
   1. Let _originalIsExtensible_ be ? _original_.[[IsExtensible]]().
   1. If _originalIsExtensible_ is *true*, throw a *TypeError* exception.
   1. Else, set _O_.[[CommittedIsExtensible]] to *false*
1. Return _booleanResult_.

[[PreventExtensions]] for proxy objects enforces the following invariants:

- The result of [[PreventExtensions]] is a Boolean value.
- [[PreventExtensions]] applied to the proxy object only returns *true* if [[IsExtensible]] applied to the proxy object's original object is *false*.


## [[GetOwnProperty]] ( _P_ )

When the [[GetOwnProperty]] internal method of a Proxy exotic object _O_ is called with property key _P_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"getOwnPropertyDescriptor"`).
1. If _trap_ is *undefined*, then
   1. Let _resultObj_ ? _original_.[[GetOwnProperty]](_P_).
1. Else,
   1. Let _resultObj_ be ? Call(_trap_, _handler_, &laquo; _original_, _P_ &raquo;).
1. If Type(_resultObj_) is neither Object nor Undefined, throw a *TypeError* exception.

1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
1. If _resultObj_ is *undefined*, then
   1. If _committedDesc_ is *undefined*, return *undefined*.

   1. If _committedDesc_.[[Configurable]] is *false*, throw a *TypeError* exception.

   1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
   1. If _extensible_ is *false*, throw a *TypeError* exception.

   1. Return *undefined*.

1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
1. Let _resultDesc_ be ? ToPropertyDescriptor(_resultObj_).
1. Call CompletePropertyDescriptor(_resultDesc_).

1. Let _valid_ be IsCompatiblePropertyDescriptor(_extensible_, _resultDesc_, _committedDesc_).
1. If _valid_ is *false*, throw a *TypeError* exception.

1. Set the value associated with the key _P_ in _O_.[[CommittedProperties]] to _committedDesc_.
1. Return _resultDesc_.

[[GetOwnProperty]] for proxy objects enforces the following invariants:

- The result of [[GetOwnProperty]] must be either an Object or *undefined*.
- A property cannot be reported as non-existent, if it has previously been observed as a non-configurable property of the proxy.
- A property cannot be reported as non-existent, if it has previously been observed as a property of the proxy and the proxy is not extensible.
- A property cannot be reported as configurable, if it has previously been observed as a non-configurable property of the proxy.
- If a property P is described as a data property with Desc.[[Value]] equal to v and Desc.[[Writable]] and Desc.[[Configurable]] are both false, then the SameValue must be returned for the Desc.[[Value]] attribute of the property on all future calls to [[GetOwnProperty]] ( P ).
- If P's attributes other than [[Writable]] may change over time or if the property might disappear, then P's [[Configurable]] attribute must be true.
- If the [[Writable]] attribute may change from false to true, then the [[Configurable]] attribute must be true.
- If the target is non-extensible and P is non-existent, then all future calls to [[GetOwnProperty]] (P) on the target must describe P as non-existent.

## [[DefineOwnProperty]] ( _P_, _Desc_ )

When the [[DefineOwnProperty]] internal method of a Proxy exotic object _O_ is called with property key _P_ and Property Descriptor _Desc_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"defineProperty"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ ? _original_.[[DefineOwnProperty]](_P_, _Desc_).
1. Else,
   1. Let _descObj_ be FromPropertyDescriptor(_Desc_).
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_, _P_, _descObj_ &raquo;)).
1. If _booleanResult_ is *false*, return *false*.

1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
1. If _committedDesc_ is *undefined*, then
   1. If _extensible_ is *false*, throw a *TypeError* exception.
1. Else _committedDesc_ is not *undefined*,
   1. If IsCompatiblePropertyDescriptor(_extensible_, _Desc_, _committedDesc_) is *false*, throw a *TypeError* exception.

1. Set the value associated with the key _P_ in _O_.[[CommittedProperties]] to _committedDesc_.
1. Return *true*.

[[DefineOwnProperty]] for proxy objects enforces the following invariants:

- The result of [[DefineOwnProperty]] is a Boolean value.
- A property cannot be added, if the target object is not extensible.
- [[DefineOwnProperty]] must return false if P has previously been observed as a non-configurable own property of the target, unless either:
   - P is a non-configurable writable own data property. A non-configurable writable data property can be changed into a non-configurable non-writable data property.
   - All attributes in Desc are the SameValue as P's attributes.

## [[HasProperty]] ( _P_ )

When the [[HasProperty]] internal method of a Proxy exotic object _O_ is called with property key _P_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"has"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ be ? _original_.[[HasProperty]](_P_).
1. Else,
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_, _P_ &raquo;)).
1. If _booleanResult_ is *false*, then
   1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
   1. If _committedDesc_ is not *undefined*, then
      1. If _committedDesc_.[[Configurable]] is *false*, throw a *TypeError* exception.

      1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
      1. If _extensible_ is *false*, throw a *TypeError* exception.

1. Return _booleanResult_.

[[HasProperty]] for proxy objects enforces the following invariants:

- The result of [[HasProperty]] is a Boolean value.
- A property cannot be reported as non-existent, if it has previously been observed as a non-configurable property of the proxy.
- A property cannot be reported as non-existent, if it has previously been observed as a property of the proxy and the proxy object is not extensible.

## [[Get]] ( _P_, _Receiver_ )

When the [[Get]] internal method of a Proxy exotic object _O_ is called with property key _P_ and ECMAScript language value _Receiver_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"get"`).
1. If _trap_ is *undefined*, then
   1. Let _result_ be ? _original_.[[Get]](_P_, _Receiver_).
1. Else,
   1. Let _result_ be ? Call(_trap_, _handler_, &laquo; _original_, _P_, _Receiver_ &raquo;).

1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
1. If _committedDesc_ is not *undefined* and _committedDesc_.[[Configurable]] is *false*, then
   1. If IsDataDescriptor(_committedDesc_) is *true* and _committedDesc_.[[Writable]] is *false*, then
      1. If SameValue(_result_, _committedDesc_.[[Value]]) is *false*, throw a *TypeError* exception.
   1. If IsAccessorDescriptor(_committedDesc_) is *true* and _committedDesc_.[[Get]] is *undefined*, then
      1. If _result_ is not *undefined*, throw a *TypeError* exception.

1. Return _result_.

[[Get]] for proxy objects enforces the following invariants:

- If P was previously observed as a non-configurable, non-writable own data property of the target with value v, then [[Get]] must return the SameValue.
- If P was previously observed as a non-configurable own accessor property of the target whose [[Get]] attribute is undefined, the [[Get]] operation must return undefined.

## [[Set]] ( _P_, _V_, _Receiver_ )

When the [[Set]] internal method of a Proxy exotic object _O_ is called with property key _P_, value _V_, and ECMAScript language value _Receiver_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"set"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ be ? _original_.[[Set]](_P_, _V_, _Receiver_).
1. Else,
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_, _P_, _V_, _Receiver_ &raquo;)).
1. If _booleanResult_ is *false*, return *false*.

1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
1. If _committedDesc_ is not *undefined* and _committedDesc_.[[Configurable]] is *false*, then
   1. If IsDataDescriptor(_committedDesc_) is *true* and _committedDesc_.[[Writable]] is *false*, then
      1. If SameValue(_V_, _committedDesc_.[[Value]]) is *false*, throw a *TypeError* exception.
   1. If IsAccessorDescriptor(_committedDesc_) is *true*, then
      1. If _committedDesc_.[[Set]] is *undefined*, throw a *TypeError* exception.

1. Return *true*.

[[Set]] for proxy objects enforces the following invariants:

- The result of [[Set]] is a Boolean value.
- If P was previously observed as a non-configurable, non-writable own data property of the target, then [[Set]] must return false unless V is the SameValue as P's [[Value]] attribute.
- If P was previously observed as a non-configurable own accessor property of the target whose [[Set]] attribute is undefined, the [[Set]] operation must return false.

## [[Delete]] ( _P_ )

When the [[Delete]] internal method of a Proxy exotic object _O_ is called with property key _P_, the following steps are taken:

1. Assert: IsPropertyKey(_P_) is *true*.
1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"deleteProperty"`).
1. If _trap_ is *undefined*, then
   1. Let _booleanResult_ be ? _original_.[[Delete]](_P_).
1. Else,
   1. Let _booleanResult_ be ToBoolean(? Call(_trap_, _handler_, &laquo; _original_, _P_ &raquo;)).
1. If _booleanResult_ is *false*, return *false*.

1. Let _committedDesc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
1. If _committedDesc_ is not *undefined* and _committedDesc_.[[Configurable]] is *false*, throw a *TypeError* exception.

1. Delete the key _P_ from _O_.[[CommittedProperties]].
1. Return *true*.

[[Delete]] for proxy objects enforces the following invariants:

- The result of [[Delete]] is a Boolean value.
- If P was previously observed to be a non-configurable own data or accessor property of the target, [[Delete]] must return false.

## [[OwnPropertyKeys]] ( )

When the [[OwnPropertyKeys]] internal method of a Proxy exotic object _O_ is called, the following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"ownKeys"`).
1. If _trap_ is *undefined*, then
   1. Let _result_ be ? _original_.[[OwnPropertyKeys]]().
   1. Assert: _result_ contains no duplicate entries.
1. Else,
   1. Let _resultArray_ be ? Call(_trap_, _handler_, &laquo; _original_ &raquo;).
   1. Let _result_ be ? CreateListFromArrayLike(_resultArray_, &laquo; String, Symbol &raquo;).
   1. If _result_ contains any duplicate entries, throw a *TypeError* exception.

1. Let _originalKeys_ be the keys of _O_.[[CommittedProperties]].
1. Assert: _originalKeys_ is a List containing only String and Symbol values.
1. Assert: _originalKeys_ contains no duplicate entries.

1. Let _configurableKeys_ be a new empty List.
1. Let _nonconfigurableKeys_ be a new empty List.
1. For each element _key_ of _originalKeys_, do
   1. Let _desc_ be the value associated with the key _P_ in _O_.[[CommittedProperties]].
   1. If _desc_.[[Configurable]] is *false*, then
      1. Append _key_ as an element of _nonconfigurableKeys_.
   1. Else,
      1. Append _key_ as an element of _configurableKeys_.

1. Let _extensible_ be _O_.[[CommittedIsExtensible]]
1. If _extensible_ is *true* and _nonconfigurableKeys_ is empty, then
   1. Return _result_.

1. Let _uncheckedResultKeys_ be a new List which is a copy of _result_.
1. For each _key_ that is an element of _nonconfigurableKeys_, do
   1. If _key_ is not an element of _uncheckedResultKeys_, throw a *TypeError* exception.
   1. Remove _key_ from _uncheckedResultKeys_.
1. If _extensible_ is *true*, return _result_.

1. For each _key_ that is an element of _configurableKeys_, do
   1. If _key_ is not an element of _uncheckedResultKeys_, throw a *TypeError* exception.
   1. Remove _key_ from _uncheckedResultKeys_.
1. If _uncheckedResultKeys_ is not empty, throw a *TypeError* exception.

1. Return _result_.

[[OwnPropertyKeys]] for proxy objects enforces the following invariants:

- The result of [[OwnPropertyKeys]] is a List.
- The returned List contains no duplicate entries.
- The Type of each result List element is either String or Symbol.
- The result List must contain the keys of all previously observed non-configurable properties of the proxy.
- If the proxy is not extensible, then the result List must contain all the keys of the previously observed properties and no other values.

## [[Call]] ( _thisArgument_, _argumentsList_ )

The [[Call]] internal method of a Proxy exotic object _O_ is called with parameters _thisArgument_ and _argumentsList_, a List of ECMAScript language values. The following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"apply"`).
1. If _trap_ is *undefined*, then
   1. Return ? Call(_original_, _thisArgument_, _argumentsList_).

1. Let _argArray_ be CreateArrayFromList(_argumentsList_).
1. Return ? Call(_trap_, _handler_, &laquo; _original_, _thisArgument_, _argArray_ &raquo;).

A Proxy exotic object only has a [[Call]] internal method if the initial value of its [[ProxyOriginal]] internal slot is an object that has a [[Call]] internal method.

## [[Construct]] ( _argumentsList_, _newTarget_ )

The [[Construct]] internal method of a Proxy exotic object _O_ is called with parameters _argumentsList_ which is a possibly empty List of ECMAScript language values and _newTarget_. The following steps are taken:

1. Let _handler_ be _O_.[[ProxyHandler]].
1. If _handler_ is *null*, throw a *TypeError* exception.
1. Assert: Type(_handler_) is Object.
1. Let _original_ be _O_.[[ProxyOriginal]].

1. Let _trap_ be ? GetMethod(_handler_, `"construct"`).
1. If _trap_ is *undefined*, then
   1. Assert: IsConstructor(_original_) is *true*.
   1. Return ? Construct(_original_, _argumentsList_, _newTarget_).

1. Let _argArray_ be CreateArrayFromList(_argumentsList_).
1. Let _newObj_ be ? Call(_trap_, _handler_, &laquo; _original_, _argArray_, _newTarget_ &raquo;).
1. If Type(_newObj_) is not Object, throw a *TypeError* exception.

1. Return _newObj_.

A Proxy exotic object only has a [[Construct]] internal method if the initial value of its [[ProxyOriginal]] internal slot is an object that has a [[Construct]] internal method.

[[Construct]] for proxy objects enforces the following invariants:

- The result of [[Construct]] must be an Object.
