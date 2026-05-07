# An alternative and more compact solution:
[for omega in [0, 3, 5, 7, 13] do
Sol:=ode::solve({u"(t) =omega²*u(t)-sinh(t),
u(0) = 0, u(1) = 0}, u(t));
plot(hold(Sol, t=0..1, #G, #L));
end_for
