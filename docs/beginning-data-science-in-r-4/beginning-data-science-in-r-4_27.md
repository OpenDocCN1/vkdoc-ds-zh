# 一些代码...
if (is.null(prior)) {
prior <- make_a_prior_distribution_somehow()
}
posterior <- update(model, prior, ...)
