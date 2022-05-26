# RankFilters

This package provides a fast implementation on rank filter using moving histogram strategy.

Rank filters have many equivalent names: _order statistic filters_, _percentile filters_, and
_rank(ed) order filters_. In functional notation, `rank(img, p)[x] = percentile(img[Ωx], p)`, or, in
naive Julia loop implementation:

```julia
using StatsBase

function rankfilter_naive(img, kernel_size, percent)
    # for illustration purposes, don't use this in practice
    out = copy(img)
    r = CartesianIndex(kernel_size .÷ 2)
    se = -r:r

    R = CartesianIndices(img)
    R_inner = (first(R) + r):(last(R) - r)
    for p in R_inner
        Ω = p .+ se
        patch = @view img[Ω]
        out[p] = percentile(vec(patch), percent)
    end

    return out
end

img = rand(0:255, 512, 512)
rankfilter(img, (3, 3), 50)  # median filter
rankfilter(img, (3, 3), 0)   # min filter
rankfilter(img, (3, 3), 100) # max filter
```

## References

- [1] Van Droogenbroeck, Marc, and Hugues Talbot. "Fast computation of morphological operations
    with arbitrary structuring elements." _Pattern recognition letters 17.14_ (1996): 1451-1460.
- [2] Van Droogenbroeck, Marc, and M. J. Buckley. "Morphological erosions and openings: fast
  algorithms based on anchors." _Journal of Mathematical Imaging and Vision 22.2_ (2005): 121-142.


[structuring element]: https://juliaimages.org/ImageMorphology.jl/stable/structuring_element/
