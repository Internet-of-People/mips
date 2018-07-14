# MIPs
Mercury Improvement Proposals (MIPs) describe standards for Mercury, including core protocol specifications, client APIs, and contract standards.

A browsable version of all current and draft MIPs can be found on [the official MIP site](http://mips.libertaria.world/).

# Contributing a new MIP

 1. Review [MIP-1](https://github.com/libertaria-project/MIPS/MIPs/mip-1.md) for description of MIP process.
 2. Fork the repository by clicking "Fork" in the top right.
 3. Add your MIP to the 'new/' folder in your fork of the repository by creating a copy of the [MIP template](https://github.com/libertaria-project/MIPS/new/mip-X.md) and giving it a descriptive name as outlined in MIP-1.
 4. Submit a Pull Request to Mercury's [MIPs repository](https://github.com/libertaria-project/mips). Your MIP will start as a PR only (unmerged) first draft in **Draft** status.

Your PR should include a first draft of the final MIP. It must meet the formatting criteria enforced by the build (largely, correct metadata in the header). If applicable, make sure you include a `discussions-to` header with the URL to any outside discussion forum or open GitHub issue where people can discuss the MIP as a whole.

When you believe your MIP is mature and ready to progress past the draft phase add a 'VOTE' label to the PR. This will put the MIP on a board where the community can vote to include it. If the Libertaria Community agrees to include it, the MIP editors will update the state of your MIP to 'Accepted' and (optionally) assigned an SDK version to target for implementation. Additionally, the PR number shall become the MIP number (MIP-n) and the PR commit shall be revised to rename and move the MIP into the MIPs folder for final storage in the MIPS repo (./MIPS/MIPs/mip-n.md)

# MIP Status Terms
* **Draft** - a MIP that is open for consideration.
* **Accepted** - a MIP that is planned for adoption, i.e. expected to be included in the next version (major/minor) of the Mercury SDK.
* **Final** - a MIP that has been implemented in an SDK release.
* **Deferred** - a MIP that is not being considered for adoption at this time. It may be reconsidered in the future by the Libertaria community.
