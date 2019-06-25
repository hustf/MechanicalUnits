# MechanicalUnits

[![Build Status](https://ci.appveyor.com/api/projects/status/github/hustf/MechanicalUnits.jl?svg=true)](https://ci.appveyor.com/project/hustf/MechanicalUnits-jl)
[![Coveralls](https://coveralls.io/repos/github/hustf/MechanicalUnits.jl/badge.svg?branch=master)](https://coveralls.io/github/hustf/MechanicalUnits.jl?branch=master)


  - [Usage](#usage)
  - [Goals](#goals)
  - [Alternatives](#alternatives)
  - [FAQ](#faq)
  - [Contributing](#contributing)
  - [License](#license)


### Low-effort calculator with units in the REPL
Units should be part of the quick side calculations mechanical and other engineers do every few minutes of a work day. But we need quick, nice and easy. That's the aim of this package, built on [Unitful.jl](https://github.com/PainterQubits/Unitful.jl).

The benefits?
* Fewer mistakes
* More pattern recognition
* Hints to find wrong input
* Quicker problem solving
* More ways to understand a problem or read a calculation
* You could pick plot recipes based on units
* You could pick table formats based on units

## Usage
Let us do some side calculations. It's a pity we can't show the colors here:
```julia
julia> using MechanicalUnits
julia> c_p = 1.00kJ/(kg*K) ; T1 = 0°C ; T2 = 1000°C ; m_air = 1kg;
julia> m_air*c_p*(T2-T1)
1000.0kJ
julia> begin
       "Work, heating air at constant pressure"
       Q_cp(T1, T2) = m_air*c_p*(T2-T1)
       end
Q_cp
julia> Q_cp(20°C, 25°C)
5.0kJ

julia> year_and_a_day = 1yr + 6*7d
(35186400//1)s
julia> 2year_and_a_day |> yr
(1086//487)yr

julia> 1dm|>upreferred
(100//1)mm
julia> exit()
PS C:\Users\F> julia --banner=no
julia> using MechanicalUnits

julia> preferunits(m)

julia> 1dm|>upreferred
(1//10)m
julia> exit()
PS C:\Users\F> julia --banner=no
julia> using MechanicalUnits
julia> # Estimate deflection
julia> E=206GPa; h = 100mm; b = 30mm; I = 1/12 * b * h^3
2.5e6mm⁴
julia> F=100kg*g; L = 2m
2m
julia> F*L^3/(3E*I) |> mm
5.0778770226537215mm

julia> # Pick a corresponding wire rope
julia> l_wire = 20m
julia> k(d) = E * 0.691 * π/4 * d^2 / l_wire |> N/mm

julia> k(30mm)
10061.845827027584N∙mm^-1

julia> δ(d)= F / k(d) |> mm
δ (generic function with 1 method)

julia> δ.([5, 6, 8]mm)
3-element Array{Float64{mm},1}:
  7.017388381199098
  4.873186375832707
 2.7411673364058977

julia> d = 6mm
ERROR: cannot assign variable Unitful.d from module Main
Stacktrace:
 [1] top-level scope at none:0

julia> dimension(d)
Time

julia> print(mech_units)
Symbol[:nm, :μm, :μm, :mm, :cm, :dm, :m, :km, :Mm, :Gm, :Tm, :Pm, 
:ns, :μs, :μs, :ms, :s, :mg, :cg, :kg, :rad, :°, :K, :Ra, :minute,
:d, :atm, :bar, :N, :daN, :kN, :MN, :Pa, :kPa, :MPa, :GPa, :J, :kJ,
:MJ, :GJ, :°C, :°F, :h, :yr, :l, :dl, :cl, :ml, :g]
```

You may get warning messages like the above when also loading other packages. If that happens, switch to importing just what you need:
```import MechanicalUnits: N, kg, m, s, MPa```


## Goals (11/19 reached)
This adaption of [Unitful.jl](https://github.com/PainterQubits/Unitful.jl) aims to be a preferable tool for quick side calculations in an office computer with limited user permissions.

This means:
* We adapt to the limitations of Windows Powershell, Julia REPL or VSCode. Substitute symbols which can't be displayed: `𝐓 -> Time`, `𝐋 -> Length`, `𝐌 -> Mass`
* Units have color, which are sort of tweakable: `show(IOContext(stderr, :unitsymbolcolor=>:bold), 1minute)`
* We pick a set of units as commonly used in mechanical industry
* `h` is an hour, not Planck's constant
* `in` is reserved by Julia; `inch` is a unit
* `g` is gravity's acceleration, not a gramme
* Prefer `mm` and `MPa`
* REPL output can always be parsed as input. We define the bullet operator `∙` (U+2219, \vysmblkcircle + tab) and print e.g. `2.32m∙s^-1`
* Export dimensions to get short type signatures:
```julia
julia> 1m |> typeof
Quantity{Int64,Length,FreeUnits{(m,),Length,nothing}}
```
* Units are never plural
* Array output moves the units outside or to the header:
```julia
julia> dist = [900mm, 1.1m]
2-element Array{Float64{mm},1}:
  900.0
 1100.0

julia> print(dist)
[900.0, 1100.0]mm
```

* We would like to:
  * tweak dimension sorting to customary order, thus: `m∙N -> N∙m`
  * support rounding and customary engineering number formatting, but in a separate package.
  * support unitful complex numbers, as they often appear while solving equations.
  * have supporting plot recipes, but in a separate package.
  * support division in a similar way as multiplication, thus: `[1,2]m/s` should work as input.
  * return, instead of an error: `10m |>s -> 10m∙s^-1∙s` 
  * support colorful units with Atom's mime type
  * register the package and have full code coverage


## Alternatives


[Unitful.jl](https://github.com/PainterQubits/Unitful.jl) lists similar adaptions for other fields.


## Am I missing some essential feature?

- **Nothing is impossible!**

- Open an [issue](https://github.com/hustf/MechanicalUnits/issues/new) and let's make this better together!

- *Bug reports, feature requests, patches, and well-wishes are always welcome.* 

## FAQ

- ***Is this for real?***

Yes. Unlike complex numbers. This is not, so far, for complex numbers. What about dual numbers? We have not tested yet.

*What does this cost?*

It costs nothing if your time is free.

## Contributing

It's the usual github way: fork, develop locally, push a commit to your fork, make a pull request.
For traceability and discussions, please make an [issue](https://github.com/hustf/MechanicalUnits/issues/new) and refer to the pull request.


## License

MechanicalUnits is released under the [MIT License](http://www.opensource.org/licenses/MIT).