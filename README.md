# Initials.jl: Canonical default initial values ("left identity") for Julia

[![Stable](https://img.shields.io/badge/docs-stable-blue.svg)](https://tkf.github.io/Initials.jl/stable)
[![Dev](https://img.shields.io/badge/docs-dev-blue.svg)](https://tkf.github.io/Initials.jl/dev)
[![Build Status](https://travis-ci.com/tkf/Initials.jl.svg?branch=master)](https://travis-ci.com/tkf/Initials.jl)
[![Codecov](https://codecov.io/gh/tkf/Initials.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/tkf/Initials.jl)
[![Coveralls](https://coveralls.io/repos/github/tkf/Initials.jl/badge.svg?branch=master)](https://coveralls.io/github/tkf/Initials.jl?branch=master)
[![Aqua QA](https://img.shields.io/badge/Aqua.jl-%F0%9F%8C%A2-aqua.svg)](https://github.com/tkf/Aqua.jl)

Initials.jl provides a generic singleton initial value `Init(f)` that
can be used as `a₀` in `f(a₀, x)`.  For a binary operator `op`, it
means that `Init(op)` acts like the left identity for _any_ type of
`x`:

```julia
julia> using Initials

julia> Init(+) + 1
1

julia> Init(+) + 1.0
1.0

julia> foldl(+, 1:3, init=Init(+))
6
```

Following methods are defined for the binary operators in `Base`:

```julia
julia> Init(*) * 1
1

julia> Init(&) & 1
1

julia> Init(|) | 1
1

julia> min(Init(min), 1)
1

julia> max(Init(max), 1)
1

julia> Base.add_sum(Init(Base.add_sum), 1)
1

julia> Base.mul_prod(Init(Base.mul_prod), 1)
1
```

`Init` is not called `Identity` because it is useful to define it for
functions that are not binary operator (symmetric in signature).  For
example, `push!!` in [BangBang.jl](https://github.com/tkf/BangBang.jl)
defines

``````julia
julia> using BangBang

julia> push!!(Init(push!!), 1)
1-element Array{Int64,1}:
 1
``````

This provides a powerful pattern when combined with `foldl`:

``````julia
julia> foldl(push!!, (1, missing, 2.0), init=Init(push!!))
3-element Array{Union{Missing, Float64},1}:
 1.0
  missing
 2.0
``````

[Transducers.jl](https://github.com/tkf/Transducers.jl) extensively
uses `Init`.

As binary operators like `*` in `Base` are heavily overloaded,
creating generic definitions such as above could have introduced
method ambiguities.  To protect against such situation, Initials.jl is
tested using [Aqua.jl](https://github.com/tkf/Aqua.jl).
