
mod commands;

use clap::{Parser, Subcommand};
use config::Config;
use commands::{greet, bRust
#[derive(Parser)]
#[command(name = "advanced")]
#[command(about = "یک CLI پیشرفته با Rust", long_about = None)]گ
    #[arg(short, long, default_value = "config
    config: String,

    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commanن
    Greet { #[arg(short, long)] name: Option<String>ن
    Bye { #[arg(short, long)] name: Option<String> },
}

fn main() {
    env_logger::init();
    let cli = Cli::parse();
    let cfg = Config::load(&cli.config);

    match cli.command {
        Commands::Greet { name } => {
            greet(&name.unwrap_or(cfg.default_name));
        }
        Commands::Bye { name } => {
            bye(&name.unwrap_or(cfg.default_name));
