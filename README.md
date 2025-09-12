use assert_cmd::Command;
use predicates::str::contains;

#[test]
fn test_greet() {
    let mut cmd = Command::cargo_bin("advanced-rust-cli")
    cmd.arg("greet").arg("--name").arg("Rahim");
    cmd.assert()
        .success()
        .stdout(contains("سلام، Rahim"));
}

#[test]
fn test_bye() {
    let mut cmd = Command::cargo_bin("advanced-rust-cli").unwrap();
    cmd.arg("bye").arg("--name").arg("Rahim");
    cmd.assert()
        .success()
        .stdout(contains("خداحافظ، Rahim"));
}
